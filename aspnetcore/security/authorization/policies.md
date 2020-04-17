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
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="55ede-103">Autorizzazione basata su criteri in ASP.NET CorePolicy-based authorization in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55ede-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55ede-104">Sotto le copertine, [l'autorizzazione basata sui ruoli](xref:security/authorization/roles) e [l'autorizzazione basata sulle attestazioni](xref:security/authorization/claims) usano un requisito, un gestore dei requisiti e un criterio preconfigurato.</span><span class="sxs-lookup"><span data-stu-id="55ede-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="55ede-105">Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice.</span><span class="sxs-lookup"><span data-stu-id="55ede-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="55ede-106">Il risultato è una struttura di autorizzazione più ricca, riutilizzabile e testata.</span><span class="sxs-lookup"><span data-stu-id="55ede-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="55ede-107">Un criterio di autorizzazione è costituito da uno o più requisiti.</span><span class="sxs-lookup"><span data-stu-id="55ede-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="55ede-108">Viene registrato come parte della configurazione del `Startup.ConfigureServices` servizio di autorizzazione, nel metodo:It's registered as part of the authorization service configuration, in the method:</span><span class="sxs-lookup"><span data-stu-id="55ede-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="55ede-109">Nell'esempio precedente viene creato un criterio "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="55ede-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="55ede-110">Ha un unico&mdash;requisito che di un'età minima, che viene fornito come parametro al requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="55ede-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="55ede-111">IAuthorizationService</span></span> 

<span data-ttu-id="55ede-112">Il servizio primario che determina <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>se l'autorizzazione ha esito positivo è:</span><span class="sxs-lookup"><span data-stu-id="55ede-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="55ede-113">Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="55ede-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="55ede-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>è un servizio marcatore senza metodi e il meccanismo per tenere traccia dell'esito positivo dell'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="55ede-115">Ciascuno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> di essi è responsabile del controllo se i requisiti sono soddisfatti:</span><span class="sxs-lookup"><span data-stu-id="55ede-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="55ede-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è ciò che il gestore utilizza per contrassegnare se i requisiti sono stati soddisfatti:The class is what the handler uses to mark whether requirements have been met:</span><span class="sxs-lookup"><span data-stu-id="55ede-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="55ede-117">Il codice seguente mostra l'implementazione predefinita semplificata (e annotata con commenti) del servizio di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="55ede-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="55ede-118">Il codice seguente `ConfigureServices`mostra un tipico :</span><span class="sxs-lookup"><span data-stu-id="55ede-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="55ede-119">Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o per l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="55ede-120">Applicazione di criteri ai controller MVC</span><span class="sxs-lookup"><span data-stu-id="55ede-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="55ede-121">Se usi pagine Razor, vedi [Applicazione di criteri alle pagine Razor](#applying-policies-to-razor-pages) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="55ede-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="55ede-122">I criteri vengono applicati `[Authorize]` ai controller utilizzando l'attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="55ede-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="55ede-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="55ede-124">Applicazione di criteri alle pagine Razor</span><span class="sxs-lookup"><span data-stu-id="55ede-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="55ede-125">I criteri vengono applicati alle `[Authorize]` pagine Razor utilizzando l'attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="55ede-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="55ede-126">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="55ede-127">I criteri possono essere applicati anche alle pagine Razor utilizzando una [convenzione](xref:security/authorization/razor-pages-authorization)di autorizzazione .</span><span class="sxs-lookup"><span data-stu-id="55ede-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="55ede-128">Requisiti</span><span class="sxs-lookup"><span data-stu-id="55ede-128">Requirements</span></span>

<span data-ttu-id="55ede-129">Un requisito di autorizzazione è una raccolta di parametri di dati che un criterio può utilizzare per valutare l'entità utente corrente.</span><span class="sxs-lookup"><span data-stu-id="55ede-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="55ede-130">Nella nostra politica "AtLeast21", il&mdash;requisito è un singolo parametro l'età minima.</span><span class="sxs-lookup"><span data-stu-id="55ede-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="55ede-131">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia del marcatore vuota.</span><span class="sxs-lookup"><span data-stu-id="55ede-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="55ede-132">Un requisito di validità minima con parametri potrebbe essere implementato come segue:</span><span class="sxs-lookup"><span data-stu-id="55ede-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="55ede-133">Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="55ede-134">In altre parole, più requisiti di autorizzazione aggiunti a un singolo criterio di autorizzazione vengono trattati su base **AND.**</span><span class="sxs-lookup"><span data-stu-id="55ede-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="55ede-135">Non è necessario che un requisito disponga di dati o proprietà.</span><span class="sxs-lookup"><span data-stu-id="55ede-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="55ede-136">Gestori di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="55ede-136">Authorization handlers</span></span>

<span data-ttu-id="55ede-137">Un gestore di autorizzazioni è responsabile della valutazione delle proprietà di un requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="55ede-138">Il gestore autorizzazioni valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornito per determinare se l'accesso è consentito.</span><span class="sxs-lookup"><span data-stu-id="55ede-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="55ede-139">Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="55ede-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="55ede-140">Un gestore può ereditare [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è necessario gestire.</span><span class="sxs-lookup"><span data-stu-id="55ede-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="55ede-141">In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="55ede-142">Usare un gestore per un requisitoUse a handler for one requirement</span><span class="sxs-lookup"><span data-stu-id="55ede-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="55ede-143">Di seguito è riportato un esempio di relazione uno-a-uno in cui un gestore di validità minima utilizza un singolo requisito:The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span><span class="sxs-lookup"><span data-stu-id="55ede-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="55ede-144">Il codice precedente determina se l'entità utente corrente ha una data di nascita che è stata emessa da un'autorità emittente nota e attendibile.</span><span class="sxs-lookup"><span data-stu-id="55ede-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="55ede-145">L'autorizzazione non può verificarsi quando l'attestazione è mancante, nel qual caso viene restituita un'attività completata.</span><span class="sxs-lookup"><span data-stu-id="55ede-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="55ede-146">Quando è presente un'attestazione, viene calcolata l'età dell'utente.</span><span class="sxs-lookup"><span data-stu-id="55ede-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="55ede-147">Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene considerata riuscita.</span><span class="sxs-lookup"><span data-stu-id="55ede-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="55ede-148">Quando l'autorizzazione `context.Succeed` ha esito positivo, viene richiamata con il requisito soddisfatto come unico parametro.</span><span class="sxs-lookup"><span data-stu-id="55ede-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="55ede-149">Usare un gestore per più requisitiUse a handler for multiple requirements</span><span class="sxs-lookup"><span data-stu-id="55ede-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="55ede-150">Di seguito è riportato un esempio di relazione uno-a-molti in cui un gestore autorizzazioni può gestire tre diversi tipi di requisiti:The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span><span class="sxs-lookup"><span data-stu-id="55ede-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="55ede-151">Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una proprietà contenente requisiti non contrassegnati come completati.</span><span class="sxs-lookup"><span data-stu-id="55ede-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="55ede-152">Per `ReadPermission` un requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="55ede-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="55ede-153">Nel caso di `EditPermission` `DeletePermission` un requisito, deve essere un proprietario per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="55ede-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="55ede-154">Registrazione del gestore</span><span class="sxs-lookup"><span data-stu-id="55ede-154">Handler registration</span></span>

<span data-ttu-id="55ede-155">I gestori vengono registrati nella raccolta di servizi durante la configurazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="55ede-156">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="55ede-157">Il codice precedente `MinimumAgeHandler` viene registrato come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="55ede-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="55ede-158">I gestori possono essere registrati utilizzando una qualsiasi delle [durate](xref:fundamentals/dependency-injection#service-lifetimes)del servizio predefinite.</span><span class="sxs-lookup"><span data-stu-id="55ede-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="55ede-159">Che cosa deve restituire un gestore?</span><span class="sxs-lookup"><span data-stu-id="55ede-159">What should a handler return?</span></span>

<span data-ttu-id="55ede-160">Si noti che il `Handle` metodo nell'esempio di [gestore](#security-authorization-handler-example) non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="55ede-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="55ede-161">Come viene indicato lo stato di esito positivo o negativo?</span><span class="sxs-lookup"><span data-stu-id="55ede-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="55ede-162">Un gestore indica `context.Succeed(IAuthorizationRequirement requirement)`l'esito positivo chiamando , passando il requisito che è stato convalidato correttamente.</span><span class="sxs-lookup"><span data-stu-id="55ede-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="55ede-163">Un gestore non è necessario gestire gli errori in genere, come altri gestori per lo stesso requisito possono avere esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="55ede-164">Per garantire l'errore, anche se `context.Fail`altri gestori dei requisiti hanno esito positivo, chiamare .</span><span class="sxs-lookup"><span data-stu-id="55ede-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="55ede-165">Se un `context.Succeed` gestore chiama o `context.Fail`, tutti gli altri gestori vengono ancora chiamati.</span><span class="sxs-lookup"><span data-stu-id="55ede-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="55ede-166">Ciò consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha convalidato o non è riuscito un requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="55ede-167">Se impostata su `false`, la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponibile in ASP.NET Core 1.1 `context.Fail` e versioni successive) ha in cortocircuito l'esecuzione dei gestori quando viene chiamata.</span><span class="sxs-lookup"><span data-stu-id="55ede-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="55ede-168">`InvokeHandlersAfterFailure`il valore `true`predefinito è , nel qual caso vengono chiamati tutti i gestori.</span><span class="sxs-lookup"><span data-stu-id="55ede-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="55ede-169">I gestori di autorizzazione vengono chiamati anche se l'autenticazione non riesce.</span><span class="sxs-lookup"><span data-stu-id="55ede-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="55ede-170">Perché dovrei volere più gestori per un requisito?</span><span class="sxs-lookup"><span data-stu-id="55ede-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="55ede-171">Nei casi in cui si desidera che la valutazione sia su base **OR,** implementare più gestori per un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="55ede-172">Ad esempio, Microsoft ha porte che si aprono solo con carte chiave.</span><span class="sxs-lookup"><span data-stu-id="55ede-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="55ede-173">Se si lascia la chiave a casa, l'addetto alla reception stampa un adesivo temporaneo e apre la porta per voi.</span><span class="sxs-lookup"><span data-stu-id="55ede-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="55ede-174">In questo scenario, si avrebbe un singolo requisito, *BuildingEntry*, ma più gestori, ognuno esaminando un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="55ede-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="55ede-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="55ede-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="55ede-178">Assicurarsi che entrambi i gestori siano [registrati.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="55ede-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="55ede-179">Se uno dei due gestori `BuildingEntryRequirement`ha esito positivo quando un criterio valuta l'oggetto , la valutazione dei criteri ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="55ede-180">Utilizzo di un func per soddisfare una politica</span><span class="sxs-lookup"><span data-stu-id="55ede-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="55ede-181">Ci possono essere situazioni in cui l'adempimento di un criterio è semplice da esprimere nel codice.</span><span class="sxs-lookup"><span data-stu-id="55ede-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="55ede-182">È possibile fornire un `Func<AuthorizationHandlerContext, bool>` quando si configurano `RequireAssertion` i criteri con il generatore di criteri.</span><span class="sxs-lookup"><span data-stu-id="55ede-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="55ede-183">Ad esempio, `BadgeEntryHandler` il precedente potrebbe essere riscritto come segue:</span><span class="sxs-lookup"><span data-stu-id="55ede-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="55ede-184">Accesso al contesto della richiesta MVC nei gestoriAccessing MVC request context in handlers</span><span class="sxs-lookup"><span data-stu-id="55ede-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="55ede-185">Il `HandleRequirementAsync` metodo implementato in un gestore `AuthorizationHandlerContext` di `TRequirement` autorizzazioni dispone di due parametri: e you are handling.</span><span class="sxs-lookup"><span data-stu-id="55ede-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="55ede-186">Framework come MVC o Jabbr sono liberi di `Resource` aggiungere `AuthorizationHandlerContext` qualsiasi oggetto alla proprietà nella per passare informazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="55ede-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="55ede-187">Ad esempio, MVC passa un'istanza `Resource` di [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) nella proprietà.</span><span class="sxs-lookup"><span data-stu-id="55ede-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="55ede-188">Questa proprietà fornisce `HttpContext` `RouteData`l'accesso a , e a tutto ciò che viene fornito da MVC e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="55ede-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="55ede-189">L'utilizzo `Resource` della proprietà è specifico del framework.</span><span class="sxs-lookup"><span data-stu-id="55ede-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="55ede-190">L'utilizzo `Resource` di informazioni nella proprietà limita i criteri di autorizzazione a framework specifici.</span><span class="sxs-lookup"><span data-stu-id="55ede-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="55ede-191">È necessario `Resource` eseguire il `is` cast della proprietà usando la parola chiave e quindi verificare che il cast sia riuscito a garantire che il codice non si arresti in modo anomalo con un quando viene eseguito su altri framework:You should cast the property using the keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span><span class="sxs-lookup"><span data-stu-id="55ede-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="55ede-192">Sotto le copertine, [l'autorizzazione basata sui ruoli](xref:security/authorization/roles) e [l'autorizzazione basata sulle attestazioni](xref:security/authorization/claims) usano un requisito, un gestore dei requisiti e un criterio preconfigurato.</span><span class="sxs-lookup"><span data-stu-id="55ede-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="55ede-193">Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice.</span><span class="sxs-lookup"><span data-stu-id="55ede-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="55ede-194">Il risultato è una struttura di autorizzazione più ricca, riutilizzabile e testata.</span><span class="sxs-lookup"><span data-stu-id="55ede-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="55ede-195">Un criterio di autorizzazione è costituito da uno o più requisiti.</span><span class="sxs-lookup"><span data-stu-id="55ede-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="55ede-196">Viene registrato come parte della configurazione del `Startup.ConfigureServices` servizio di autorizzazione, nel metodo:It's registered as part of the authorization service configuration, in the method:</span><span class="sxs-lookup"><span data-stu-id="55ede-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="55ede-197">Nell'esempio precedente viene creato un criterio "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="55ede-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="55ede-198">Ha un unico&mdash;requisito che di un'età minima, che viene fornito come parametro al requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="55ede-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="55ede-199">IAuthorizationService</span></span> 

<span data-ttu-id="55ede-200">Il servizio primario che determina <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>se l'autorizzazione ha esito positivo è:</span><span class="sxs-lookup"><span data-stu-id="55ede-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="55ede-201">Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="55ede-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="55ede-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>è un servizio marcatore senza metodi e il meccanismo per tenere traccia dell'esito positivo dell'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="55ede-203">Ciascuno <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> di essi è responsabile del controllo se i requisiti sono soddisfatti:</span><span class="sxs-lookup"><span data-stu-id="55ede-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="55ede-204">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è ciò che il gestore utilizza per contrassegnare se i requisiti sono stati soddisfatti:The class is what the handler uses to mark whether requirements have been met:</span><span class="sxs-lookup"><span data-stu-id="55ede-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="55ede-205">Il codice seguente mostra l'implementazione predefinita semplificata (e annotata con commenti) del servizio di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="55ede-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="55ede-206">Il codice seguente `ConfigureServices`mostra un tipico :</span><span class="sxs-lookup"><span data-stu-id="55ede-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="55ede-207">Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` o per l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="55ede-208">Applicazione di criteri ai controller MVC</span><span class="sxs-lookup"><span data-stu-id="55ede-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="55ede-209">Se usi pagine Razor, vedi [Applicazione di criteri alle pagine Razor](#applying-policies-to-razor-pages) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="55ede-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="55ede-210">I criteri vengono applicati `[Authorize]` ai controller utilizzando l'attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="55ede-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="55ede-211">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="55ede-212">Applicazione di criteri alle pagine Razor</span><span class="sxs-lookup"><span data-stu-id="55ede-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="55ede-213">I criteri vengono applicati alle `[Authorize]` pagine Razor utilizzando l'attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="55ede-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="55ede-214">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="55ede-215">I criteri possono essere applicati anche alle pagine Razor utilizzando una [convenzione](xref:security/authorization/razor-pages-authorization)di autorizzazione .</span><span class="sxs-lookup"><span data-stu-id="55ede-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="55ede-216">Requisiti</span><span class="sxs-lookup"><span data-stu-id="55ede-216">Requirements</span></span>

<span data-ttu-id="55ede-217">Un requisito di autorizzazione è una raccolta di parametri di dati che un criterio può utilizzare per valutare l'entità utente corrente.</span><span class="sxs-lookup"><span data-stu-id="55ede-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="55ede-218">Nella nostra politica "AtLeast21", il&mdash;requisito è un singolo parametro l'età minima.</span><span class="sxs-lookup"><span data-stu-id="55ede-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="55ede-219">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia del marcatore vuota.</span><span class="sxs-lookup"><span data-stu-id="55ede-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="55ede-220">Un requisito di validità minima con parametri potrebbe essere implementato come segue:</span><span class="sxs-lookup"><span data-stu-id="55ede-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="55ede-221">Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="55ede-222">In altre parole, più requisiti di autorizzazione aggiunti a un singolo criterio di autorizzazione vengono trattati su base **AND.**</span><span class="sxs-lookup"><span data-stu-id="55ede-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="55ede-223">Non è necessario che un requisito disponga di dati o proprietà.</span><span class="sxs-lookup"><span data-stu-id="55ede-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="55ede-224">Gestori di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="55ede-224">Authorization handlers</span></span>

<span data-ttu-id="55ede-225">Un gestore di autorizzazioni è responsabile della valutazione delle proprietà di un requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="55ede-226">Il gestore autorizzazioni valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fornito per determinare se l'accesso è consentito.</span><span class="sxs-lookup"><span data-stu-id="55ede-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="55ede-227">Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="55ede-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="55ede-228">Un gestore può ereditare [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è necessario gestire.</span><span class="sxs-lookup"><span data-stu-id="55ede-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="55ede-229">In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="55ede-230">Usare un gestore per un requisitoUse a handler for one requirement</span><span class="sxs-lookup"><span data-stu-id="55ede-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="55ede-231">Di seguito è riportato un esempio di relazione uno-a-uno in cui un gestore di validità minima utilizza un singolo requisito:The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span><span class="sxs-lookup"><span data-stu-id="55ede-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="55ede-232">Il codice precedente determina se l'entità utente corrente ha una data di nascita che è stata emessa da un'autorità emittente nota e attendibile.</span><span class="sxs-lookup"><span data-stu-id="55ede-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="55ede-233">L'autorizzazione non può verificarsi quando l'attestazione è mancante, nel qual caso viene restituita un'attività completata.</span><span class="sxs-lookup"><span data-stu-id="55ede-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="55ede-234">Quando è presente un'attestazione, viene calcolata l'età dell'utente.</span><span class="sxs-lookup"><span data-stu-id="55ede-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="55ede-235">Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene considerata riuscita.</span><span class="sxs-lookup"><span data-stu-id="55ede-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="55ede-236">Quando l'autorizzazione `context.Succeed` ha esito positivo, viene richiamata con il requisito soddisfatto come unico parametro.</span><span class="sxs-lookup"><span data-stu-id="55ede-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="55ede-237">Usare un gestore per più requisitiUse a handler for multiple requirements</span><span class="sxs-lookup"><span data-stu-id="55ede-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="55ede-238">Di seguito è riportato un esempio di relazione uno-a-molti in cui un gestore autorizzazioni può gestire tre diversi tipi di requisiti:The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span><span class="sxs-lookup"><span data-stu-id="55ede-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="55ede-239">Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;una proprietà contenente requisiti non contrassegnati come completati.</span><span class="sxs-lookup"><span data-stu-id="55ede-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="55ede-240">Per `ReadPermission` un requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="55ede-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="55ede-241">Nel caso di `EditPermission` `DeletePermission` un requisito, deve essere un proprietario per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="55ede-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="55ede-242">Registrazione del gestore</span><span class="sxs-lookup"><span data-stu-id="55ede-242">Handler registration</span></span>

<span data-ttu-id="55ede-243">I gestori vengono registrati nella raccolta di servizi durante la configurazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="55ede-244">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="55ede-245">Il codice precedente `MinimumAgeHandler` viene registrato come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="55ede-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="55ede-246">I gestori possono essere registrati utilizzando una qualsiasi delle [durate](xref:fundamentals/dependency-injection#service-lifetimes)del servizio predefinite.</span><span class="sxs-lookup"><span data-stu-id="55ede-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="55ede-247">Che cosa deve restituire un gestore?</span><span class="sxs-lookup"><span data-stu-id="55ede-247">What should a handler return?</span></span>

<span data-ttu-id="55ede-248">Si noti che il `Handle` metodo nell'esempio di [gestore](#security-authorization-handler-example) non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="55ede-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="55ede-249">Come viene indicato lo stato di esito positivo o negativo?</span><span class="sxs-lookup"><span data-stu-id="55ede-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="55ede-250">Un gestore indica `context.Succeed(IAuthorizationRequirement requirement)`l'esito positivo chiamando , passando il requisito che è stato convalidato correttamente.</span><span class="sxs-lookup"><span data-stu-id="55ede-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="55ede-251">Un gestore non è necessario gestire gli errori in genere, come altri gestori per lo stesso requisito possono avere esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="55ede-252">Per garantire l'errore, anche se `context.Fail`altri gestori dei requisiti hanno esito positivo, chiamare .</span><span class="sxs-lookup"><span data-stu-id="55ede-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="55ede-253">Se un `context.Succeed` gestore chiama o `context.Fail`, tutti gli altri gestori vengono ancora chiamati.</span><span class="sxs-lookup"><span data-stu-id="55ede-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="55ede-254">Ciò consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha convalidato o non è riuscito un requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="55ede-255">Se impostata su `false`, la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (disponibile in ASP.NET Core 1.1 `context.Fail` e versioni successive) ha in cortocircuito l'esecuzione dei gestori quando viene chiamata.</span><span class="sxs-lookup"><span data-stu-id="55ede-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="55ede-256">`InvokeHandlersAfterFailure`il valore `true`predefinito è , nel qual caso vengono chiamati tutti i gestori.</span><span class="sxs-lookup"><span data-stu-id="55ede-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="55ede-257">I gestori di autorizzazione vengono chiamati anche se l'autenticazione non riesce.</span><span class="sxs-lookup"><span data-stu-id="55ede-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="55ede-258">Perché dovrei volere più gestori per un requisito?</span><span class="sxs-lookup"><span data-stu-id="55ede-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="55ede-259">Nei casi in cui si desidera che la valutazione sia su base **OR,** implementare più gestori per un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="55ede-260">Ad esempio, Microsoft ha porte che si aprono solo con carte chiave.</span><span class="sxs-lookup"><span data-stu-id="55ede-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="55ede-261">Se si lascia la chiave a casa, l'addetto alla reception stampa un adesivo temporaneo e apre la porta per voi.</span><span class="sxs-lookup"><span data-stu-id="55ede-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="55ede-262">In questo scenario, si avrebbe un singolo requisito, *BuildingEntry*, ma più gestori, ognuno esaminando un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="55ede-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="55ede-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="55ede-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="55ede-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="55ede-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="55ede-266">Assicurarsi che entrambi i gestori siano [registrati.](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)</span><span class="sxs-lookup"><span data-stu-id="55ede-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="55ede-267">Se uno dei due gestori `BuildingEntryRequirement`ha esito positivo quando un criterio valuta l'oggetto , la valutazione dei criteri ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="55ede-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="55ede-268">Utilizzo di un func per soddisfare una politica</span><span class="sxs-lookup"><span data-stu-id="55ede-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="55ede-269">Ci possono essere situazioni in cui l'adempimento di un criterio è semplice da esprimere nel codice.</span><span class="sxs-lookup"><span data-stu-id="55ede-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="55ede-270">È possibile fornire un `Func<AuthorizationHandlerContext, bool>` quando si configurano `RequireAssertion` i criteri con il generatore di criteri.</span><span class="sxs-lookup"><span data-stu-id="55ede-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="55ede-271">Ad esempio, `BadgeEntryHandler` il precedente potrebbe essere riscritto come segue:</span><span class="sxs-lookup"><span data-stu-id="55ede-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="55ede-272">Accesso al contesto della richiesta MVC nei gestoriAccessing MVC request context in handlers</span><span class="sxs-lookup"><span data-stu-id="55ede-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="55ede-273">Il `HandleRequirementAsync` metodo implementato in un gestore `AuthorizationHandlerContext` di `TRequirement` autorizzazioni dispone di due parametri: e you are handling.</span><span class="sxs-lookup"><span data-stu-id="55ede-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="55ede-274">Framework come MVC o SignalR sono liberi di `Resource` aggiungere `AuthorizationHandlerContext` qualsiasi oggetto alla proprietà nella per passare informazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="55ede-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="55ede-275">Quando si utilizza il routing degli endpoint, l'autorizzazione viene in genere gestita dal middleware di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="55ede-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="55ede-276">In questo caso, la `Resource` proprietà <xref:Microsoft.AspNetCore.Http.Endpoint>è un'istanza di .</span><span class="sxs-lookup"><span data-stu-id="55ede-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="55ede-277">L'endpoint può essere utilizzato per sondare la risorsa sottostante a cui si sta instradando.</span><span class="sxs-lookup"><span data-stu-id="55ede-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="55ede-278">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="55ede-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="55ede-279">Con il routing tradizionale o quando l'autorizzazione viene eseguita `Resource` come <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> parte del filtro di autorizzazione di MVC, il valore di è un'istanza.</span><span class="sxs-lookup"><span data-stu-id="55ede-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="55ede-280">Questa proprietà fornisce `HttpContext` `RouteData`l'accesso a , e a tutto ciò che viene fornito da MVC e Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="55ede-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="55ede-281">L'utilizzo `Resource` della proprietà è specifico del framework.</span><span class="sxs-lookup"><span data-stu-id="55ede-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="55ede-282">L'utilizzo `Resource` di informazioni nella proprietà limita i criteri di autorizzazione a framework specifici.</span><span class="sxs-lookup"><span data-stu-id="55ede-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="55ede-283">È necessario `Resource` eseguire il `is` cast della proprietà usando la parola chiave e quindi verificare che il cast sia riuscito a garantire che il codice non si arresti in modo anomalo con un quando viene eseguito su altri framework:You should cast the property using the keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span><span class="sxs-lookup"><span data-stu-id="55ede-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
