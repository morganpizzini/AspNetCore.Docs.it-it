---
title: Autorizzazione basata su criteri in ASP.NET CorePolicy-based authorization in ASP.NET Core
author: rick-anderson
description: Informazioni su come creare e usare gestori di criteri di autorizzazione per applicare i requisiti di autorizzazione in un'app ASP.NET Core.Learn how to create and use authorization policy handlers for enforcing authorization requirements in an ASP.NET Core app.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488761"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorizzazione basata su criteri in ASP.NET CorePolicy-based authorization in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Sotto le copertine, [l'autorizzazione basata sui ruoli](xref:security/authorization/roles) e [l'autorizzazione basata sulle attestazioni](xref:security/authorization/claims) usano un requisito, un gestore dei requisiti e un criterio preconfigurato. Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice. Il risultato è una struttura di autorizzazione più ricca, riutilizzabile e testata.

Un criterio di autorizzazione è costituito da uno o più requisiti. Viene registrato come parte della configurazione del `Startup.ConfigureServices` servizio di autorizzazione, nel metodo:It's registered as part of the authorization service configuration, in the method:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Nell'esempio precedente viene creato un criterio "AtLeast21". Ha un unico&mdash;requisito che di un'età minima, che viene fornito come parametro al requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Il servizio primario che determina <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>se l'autorizzazione ha esito positivo è:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>è un servizio marcatore senza metodi e il meccanismo per tenere traccia dell'esito positivo dell'autorizzazione.

Ciascuno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> di essi è responsabile del controllo se i requisiti sono soddisfatti:
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è ciò che il gestore utilizza per contrassegnare se i requisiti sono stati soddisfatti:The class is what the handler uses to mark whether requirements have been met:

```csharp
 context.Succeed(requirement)
```

Il codice seguente mostra l'implementazione predefinita semplificata (e annotata con commenti) del servizio di autorizzazione:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Il codice seguente `ConfigureServices`mostra un tipico :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o per l'autorizzazione.

## <a name="applying-policies-to-mvc-controllers"></a>Applicazione di criteri ai controller MVC

Se usi pagine Razor, vedi [Applicazione di criteri alle pagine Razor](#applying-policies-to-razor-pages) in questo documento.

I criteri vengono applicati `[Authorize]` ai controller utilizzando l'attributo con il nome del criterio. Ad esempio:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Applicazione di criteri alle pagine Razor

I criteri vengono applicati alle `[Authorize]` pagine Razor utilizzando l'attributo con il nome del criterio. Ad esempio:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

I criteri possono essere applicati anche alle pagine Razor utilizzando una [convenzione](xref:security/authorization/razor-pages-authorization)di autorizzazione .

## <a name="requirements"></a>Requisiti

Un requisito di autorizzazione è una raccolta di parametri di dati che un criterio può utilizzare per valutare l'entità utente corrente. Nella nostra politica "AtLeast21", il&mdash;requisito è un singolo parametro l'età minima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia del marcatore vuota. Un requisito di validità minima con parametri potrebbe essere implementato come segue:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo. In altre parole, più requisiti di autorizzazione aggiunti a un singolo criterio di autorizzazione vengono trattati su base **AND.**

> [!NOTE]
> Non è necessario che un requisito disponga di dati o proprietà.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestori di autorizzazione

Un gestore di autorizzazioni è responsabile della valutazione delle proprietà di un requisito. Il gestore autorizzazioni valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornito per determinare se l'accesso è consentito.

Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers). Un gestore può ereditare [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è necessario gestire. In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usare un gestore per un requisitoUse a handler for one requirement

<a name="security-authorization-handler-example"></a>

Di seguito è riportato un esempio di relazione uno-a-uno in cui un gestore di validità minima utilizza un singolo requisito:The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Il codice precedente determina se l'entità utente corrente ha una data di nascita che è stata emessa da un'autorità emittente nota e attendibile. L'autorizzazione non può verificarsi quando l'attestazione è mancante, nel qual caso viene restituita un'attività completata. Quando è presente un'attestazione, viene calcolata l'età dell'utente. Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene considerata riuscita. Quando l'autorizzazione `context.Succeed` ha esito positivo, viene richiamata con il requisito soddisfatto come unico parametro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usare un gestore per più requisitiUse a handler for multiple requirements

Di seguito è riportato un esempio di relazione uno-a-molti in cui un gestore autorizzazioni può gestire tre diversi tipi di requisiti:The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una proprietà contenente requisiti non contrassegnati come completati. Per `ReadPermission` un requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta. Nel caso di `EditPermission` `DeletePermission` un requisito, deve essere un proprietario per accedere alla risorsa richiesta.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrazione del gestore

I gestori vengono registrati nella raccolta di servizi durante la configurazione. Ad esempio:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Il codice precedente `MinimumAgeHandler` viene registrato come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. I gestori possono essere registrati utilizzando una qualsiasi delle [durate](xref:fundamentals/dependency-injection#service-lifetimes)del servizio predefinite.

## <a name="what-should-a-handler-return"></a>Che cosa deve restituire un gestore?

Si noti che il `Handle` metodo nell'esempio di [gestore](#security-authorization-handler-example) non restituisce alcun valore. Come viene indicato lo stato di esito positivo o negativo?

* Un gestore indica `context.Succeed(IAuthorizationRequirement requirement)`l'esito positivo chiamando , passando il requisito che è stato convalidato correttamente.

* Un gestore non è necessario gestire gli errori in genere, come altri gestori per lo stesso requisito possono avere esito positivo.

* Per garantire l'errore, anche se `context.Fail`altri gestori dei requisiti hanno esito positivo, chiamare .

Se un `context.Succeed` gestore chiama o `context.Fail`, tutti gli altri gestori vengono ancora chiamati. Ciò consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha convalidato o non è riuscito un requisito. Se impostata su `false`, la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponibile in ASP.NET Core 1.1 `context.Fail` e versioni successive) ha in cortocircuito l'esecuzione dei gestori quando viene chiamata. `InvokeHandlersAfterFailure`il valore `true`predefinito è , nel qual caso vengono chiamati tutti i gestori.

> [!NOTE]
> I gestori di autorizzazione vengono chiamati anche se l'autenticazione non riesce.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Perché dovrei volere più gestori per un requisito?

Nei casi in cui si desidera che la valutazione sia su base **OR,** implementare più gestori per un singolo requisito. Ad esempio, Microsoft ha porte che si aprono solo con carte chiave. Se si lascia la chiave a casa, l'addetto alla reception stampa un adesivo temporaneo e apre la porta per voi. In questo scenario, si avrebbe un singolo requisito, *BuildingEntry*, ma più gestori, ognuno esaminando un singolo requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Assicurarsi che entrambi i gestori siano [registrati.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Se uno dei due gestori `BuildingEntryRequirement`ha esito positivo quando un criterio valuta l'oggetto , la valutazione dei criteri ha esito positivo.

## <a name="using-a-func-to-fulfill-a-policy"></a>Utilizzo di un func per soddisfare una politica

Ci possono essere situazioni in cui l'adempimento di un criterio è semplice da esprimere nel codice. È possibile fornire un `Func<AuthorizationHandlerContext, bool>` quando si configurano `RequireAssertion` i criteri con il generatore di criteri.

Ad esempio, `BadgeEntryHandler` il precedente potrebbe essere riscritto come segue:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Accesso al contesto della richiesta MVC nei gestoriAccessing MVC request context in handlers

Il `HandleRequirementAsync` metodo implementato in un gestore `AuthorizationHandlerContext` di `TRequirement` autorizzazioni dispone di due parametri: e you are handling. Framework come MVC o Jabbr sono liberi di `Resource` aggiungere `AuthorizationHandlerContext` qualsiasi oggetto alla proprietà nella per passare informazioni aggiuntive.

Ad esempio, MVC passa un'istanza `Resource` di [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) nella proprietà. Questa proprietà fornisce `HttpContext` `RouteData`l'accesso a , e a tutto ciò che viene fornito da MVC e Razor Pages.

L'utilizzo `Resource` della proprietà è specifico del framework. L'utilizzo `Resource` di informazioni nella proprietà limita i criteri di autorizzazione a framework specifici. È necessario `Resource` eseguire il `is` cast della proprietà usando la parola chiave e quindi verificare che il cast sia riuscito a garantire che il codice non si arresti in modo anomalo con un quando viene eseguito su altri framework:You should cast the property using the keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

Sotto le copertine, [l'autorizzazione basata sui ruoli](xref:security/authorization/roles) e [l'autorizzazione basata sulle attestazioni](xref:security/authorization/claims) usano un requisito, un gestore dei requisiti e un criterio preconfigurato. Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice. Il risultato è una struttura di autorizzazione più ricca, riutilizzabile e testata.

Un criterio di autorizzazione è costituito da uno o più requisiti. Viene registrato come parte della configurazione del `Startup.ConfigureServices` servizio di autorizzazione, nel metodo:It's registered as part of the authorization service configuration, in the method:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Nell'esempio precedente viene creato un criterio "AtLeast21". Ha un unico&mdash;requisito che di un'età minima, che viene fornito come parametro al requisito.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Il servizio primario che determina <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>se l'autorizzazione ha esito positivo è:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>è un servizio marcatore senza metodi e il meccanismo per tenere traccia dell'esito positivo dell'autorizzazione.

Ciascuno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> di essi è responsabile del controllo se i requisiti sono soddisfatti:
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è ciò che il gestore utilizza per contrassegnare se i requisiti sono stati soddisfatti:The class is what the handler uses to mark whether requirements have been met:

```csharp
 context.Succeed(requirement)
```

Il codice seguente mostra l'implementazione predefinita semplificata (e annotata con commenti) del servizio di autorizzazione:

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Il codice seguente `ConfigureServices`mostra un tipico :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o per l'autorizzazione.

## <a name="applying-policies-to-mvc-controllers"></a>Applicazione di criteri ai controller MVC

Se usi pagine Razor, vedi [Applicazione di criteri alle pagine Razor](#applying-policies-to-razor-pages) in questo documento.

I criteri vengono applicati `[Authorize]` ai controller utilizzando l'attributo con il nome del criterio. Ad esempio:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Applicazione di criteri alle pagine Razor

I criteri vengono applicati alle `[Authorize]` pagine Razor utilizzando l'attributo con il nome del criterio. Ad esempio:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

I criteri possono essere applicati anche alle pagine Razor utilizzando una [convenzione](xref:security/authorization/razor-pages-authorization)di autorizzazione .

## <a name="requirements"></a>Requisiti

Un requisito di autorizzazione è una raccolta di parametri di dati che un criterio può utilizzare per valutare l'entità utente corrente. Nella nostra politica "AtLeast21", il&mdash;requisito è un singolo parametro l'età minima. Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia del marcatore vuota. Un requisito di validità minima con parametri potrebbe essere implementato come segue:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo. In altre parole, più requisiti di autorizzazione aggiunti a un singolo criterio di autorizzazione vengono trattati su base **AND.**

> [!NOTE]
> Non è necessario che un requisito disponga di dati o proprietà.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestori di autorizzazione

Un gestore di autorizzazioni è responsabile della valutazione delle proprietà di un requisito. Il gestore autorizzazioni valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornito per determinare se l'accesso è consentito.

Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers). Un gestore può ereditare [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è necessario gestire. In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.

### <a name="use-a-handler-for-one-requirement"></a>Usare un gestore per un requisitoUse a handler for one requirement

<a name="security-authorization-handler-example"></a>

Di seguito è riportato un esempio di relazione uno-a-uno in cui un gestore di validità minima utilizza un singolo requisito:The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Il codice precedente determina se l'entità utente corrente ha una data di nascita che è stata emessa da un'autorità emittente nota e attendibile. L'autorizzazione non può verificarsi quando l'attestazione è mancante, nel qual caso viene restituita un'attività completata. Quando è presente un'attestazione, viene calcolata l'età dell'utente. Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene considerata riuscita. Quando l'autorizzazione `context.Succeed` ha esito positivo, viene richiamata con il requisito soddisfatto come unico parametro.

### <a name="use-a-handler-for-multiple-requirements"></a>Usare un gestore per più requisitiUse a handler for multiple requirements

Di seguito è riportato un esempio di relazione uno-a-molti in cui un gestore autorizzazioni può gestire tre diversi tipi di requisiti:The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una proprietà contenente requisiti non contrassegnati come completati. Per `ReadPermission` un requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta. Nel caso di `EditPermission` `DeletePermission` un requisito, deve essere un proprietario per accedere alla risorsa richiesta.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrazione del gestore

I gestori vengono registrati nella raccolta di servizi durante la configurazione. Ad esempio:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Il codice precedente `MinimumAgeHandler` viene registrato come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. I gestori possono essere registrati utilizzando una qualsiasi delle [durate](xref:fundamentals/dependency-injection#service-lifetimes)del servizio predefinite.

## <a name="what-should-a-handler-return"></a>Che cosa deve restituire un gestore?

Si noti che il `Handle` metodo nell'esempio di [gestore](#security-authorization-handler-example) non restituisce alcun valore. Come viene indicato lo stato di esito positivo o negativo?

* Un gestore indica `context.Succeed(IAuthorizationRequirement requirement)`l'esito positivo chiamando , passando il requisito che è stato convalidato correttamente.

* Un gestore non è necessario gestire gli errori in genere, come altri gestori per lo stesso requisito possono avere esito positivo.

* Per garantire l'errore, anche se `context.Fail`altri gestori dei requisiti hanno esito positivo, chiamare .

Se un `context.Succeed` gestore chiama o `context.Fail`, tutti gli altri gestori vengono ancora chiamati. Ciò consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha convalidato o non è riuscito un requisito. Se impostata su `false`, la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponibile in ASP.NET Core 1.1 `context.Fail` e versioni successive) ha in cortocircuito l'esecuzione dei gestori quando viene chiamata. `InvokeHandlersAfterFailure`il valore `true`predefinito è , nel qual caso vengono chiamati tutti i gestori.

> [!NOTE]
> I gestori di autorizzazione vengono chiamati anche se l'autenticazione non riesce.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Perché dovrei volere più gestori per un requisito?

Nei casi in cui si desidera che la valutazione sia su base **OR,** implementare più gestori per un singolo requisito. Ad esempio, Microsoft ha porte che si aprono solo con carte chiave. Se si lascia la chiave a casa, l'addetto alla reception stampa un adesivo temporaneo e apre la porta per voi. In questo scenario, si avrebbe un singolo requisito, *BuildingEntry*, ma più gestori, ognuno esaminando un singolo requisito.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Assicurarsi che entrambi i gestori siano [registrati.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) Se uno dei due gestori `BuildingEntryRequirement`ha esito positivo quando un criterio valuta l'oggetto , la valutazione dei criteri ha esito positivo.

## <a name="using-a-func-to-fulfill-a-policy"></a>Utilizzo di un func per soddisfare una politica

Ci possono essere situazioni in cui l'adempimento di un criterio è semplice da esprimere nel codice. È possibile fornire un `Func<AuthorizationHandlerContext, bool>` quando si configurano `RequireAssertion` i criteri con il generatore di criteri.

Ad esempio, `BadgeEntryHandler` il precedente potrebbe essere riscritto come segue:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Accesso al contesto della richiesta MVC nei gestoriAccessing MVC request context in handlers

Il `HandleRequirementAsync` metodo implementato in un gestore `AuthorizationHandlerContext` di `TRequirement` autorizzazioni dispone di due parametri: e you are handling. Framework come MVC o SignalR sono liberi di `Resource` aggiungere `AuthorizationHandlerContext` qualsiasi oggetto alla proprietà nella per passare informazioni aggiuntive.

Quando si utilizza il routing degli endpoint, l'autorizzazione viene in genere gestita dal middleware di autorizzazione. In questo caso, la `Resource` proprietà <xref:Microsoft.AspNetCore.Http.Endpoint>è un'istanza di . L'endpoint può essere utilizzato per sondare la risorsa sottostante a cui si sta instradando. Ad esempio:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Con il routing tradizionale o quando l'autorizzazione viene eseguita `Resource` come <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> parte del filtro di autorizzazione di MVC, il valore di è un'istanza. Questa proprietà fornisce `HttpContext` `RouteData`l'accesso a , e a tutto ciò che viene fornito da MVC e Razor Pages.

L'utilizzo `Resource` della proprietà è specifico del framework. L'utilizzo `Resource` di informazioni nella proprietà limita i criteri di autorizzazione a framework specifici. È necessario `Resource` eseguire il `is` cast della proprietà usando la parola chiave e quindi verificare che il cast sia riuscito a garantire che il codice non si arresti in modo anomalo con un quando viene eseguito su altri framework:You should cast the property using the keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
