---
title: Autorizzazione basata su criteri in ASP.NET Core
author: rick-anderson
description: Informazioni su come creare e usare i gestori dei criteri di autorizzazione per applicare i requisiti di autorizzazione in un'app ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 140969c80191c9549bc19507b9c4818b88fed0d1
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636803"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="b0595-103">Autorizzazione basata su criteri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0595-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b0595-104">Dietro le quinte, l'autorizzazione [basata sui ruoli](xref:security/authorization/roles) e l' [autorizzazione basata sulle attestazioni](xref:security/authorization/claims) utilizzano un requisito, un gestore requisiti e un criterio preconfigurato.</span><span class="sxs-lookup"><span data-stu-id="b0595-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b0595-105">Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice.</span><span class="sxs-lookup"><span data-stu-id="b0595-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b0595-106">Il risultato è una struttura di autorizzazione verificabile, riutilizzabile e più ricca.</span><span class="sxs-lookup"><span data-stu-id="b0595-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b0595-107">Un criterio di autorizzazione è costituito da uno o più requisiti.</span><span class="sxs-lookup"><span data-stu-id="b0595-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b0595-108">Viene registrato come parte della configurazione del servizio di autorizzazione, nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="b0595-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="b0595-109">Nell'esempio precedente viene creato un criterio "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b0595-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b0595-110">Ha un singolo requisito &mdash; che di età minima, fornito come parametro al requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b0595-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b0595-111">IAuthorizationService</span></span>

<span data-ttu-id="b0595-112">Il servizio primario che determina se l'autorizzazione ha esito positivo è <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="b0595-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b0595-113">Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b0595-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b0595-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> è un servizio marcatore senza metodi e il meccanismo per verificare se l'autorizzazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b0595-115">Ogni <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> è responsabile di verificare se sono soddisfatti i requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b0595-116">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è l'elemento usato dal gestore per contrassegnare se sono stati soddisfatti i requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b0595-117">Nel codice seguente viene illustrata l'implementazione predefinita del servizio di autorizzazione semplificata (e annotata con commenti):</span><span class="sxs-lookup"><span data-stu-id="b0595-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b0595-118">Il codice seguente illustra un tipico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b0595-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b0595-119">Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` per l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b0595-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="b0595-120">Applicare criteri ai controller MVC</span><span class="sxs-lookup"><span data-stu-id="b0595-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="b0595-121">Se si usano le Razor pagine, vedere [applicare i criteri alle Razor pagine](#apply-policies-to-razor-pages) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b0595-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b0595-122">I criteri vengono applicati ai controller usando l' `[Authorize]` attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="b0595-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0595-123">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="b0595-124">Applicare criteri alle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="b0595-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="b0595-125">I criteri vengono applicati alle Razor pagine usando l' `[Authorize]` attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="b0595-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0595-126">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b0595-127">I criteri ***non*** possono essere applicati a Razor livello di gestore di pagina, ma devono essere applicati alla pagina.</span><span class="sxs-lookup"><span data-stu-id="b0595-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="b0595-128">I criteri possono essere applicati alle Razor pagine utilizzando una [convenzione di autorizzazione](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b0595-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b0595-129">Requisiti</span><span class="sxs-lookup"><span data-stu-id="b0595-129">Requirements</span></span>

<span data-ttu-id="b0595-130">Un requisito di autorizzazione è una raccolta di parametri dati che un criterio può utilizzare per valutare l'entità utente corrente.</span><span class="sxs-lookup"><span data-stu-id="b0595-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b0595-131">Nel criterio "AtLeast21", il requisito è un singolo parametro &mdash; la validità minima.</span><span class="sxs-lookup"><span data-stu-id="b0595-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b0595-132">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia marcatore vuota.</span><span class="sxs-lookup"><span data-stu-id="b0595-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b0595-133">Un requisito di validità minima con parametri può essere implementato come segue:</span><span class="sxs-lookup"><span data-stu-id="b0595-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b0595-134">Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b0595-135">In altre parole, i requisiti di autorizzazione multipli aggiunti a un singolo criterio di autorizzazione vengono gestiti in base a **e** .</span><span class="sxs-lookup"><span data-stu-id="b0595-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b0595-136">Un requisito non deve avere dati o proprietà.</span><span class="sxs-lookup"><span data-stu-id="b0595-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b0595-137">Gestori autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="b0595-137">Authorization handlers</span></span>

<span data-ttu-id="b0595-138">Un gestore di autorizzazione è responsabile della valutazione delle proprietà di un requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b0595-139">Il gestore di autorizzazione valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) specificato per determinare se l'accesso è consentito.</span><span class="sxs-lookup"><span data-stu-id="b0595-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b0595-140">Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b0595-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b0595-141">Un gestore può ereditare [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è il requisito da gestire.</span><span class="sxs-lookup"><span data-stu-id="b0595-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b0595-142">In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b0595-143">Usare un gestore per un requisito</span><span class="sxs-lookup"><span data-stu-id="b0595-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b0595-144">Di seguito è riportato un esempio di una relazione uno-a-uno in cui un gestore età minima utilizza un singolo requisito:</span><span class="sxs-lookup"><span data-stu-id="b0595-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b0595-145">Il codice precedente determina se l'entità utente corrente ha una data di attestazione di nascita che è stata rilasciata da un'autorità emittente nota ed attendibile.</span><span class="sxs-lookup"><span data-stu-id="b0595-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b0595-146">L'autorizzazione non può verificarsi quando l'attestazione non è presente, nel qual caso viene restituita un'attività completata.</span><span class="sxs-lookup"><span data-stu-id="b0595-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b0595-147">Quando è presente un'attestazione, viene calcolata l'età dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b0595-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b0595-148">Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene ritenuta corretta.</span><span class="sxs-lookup"><span data-stu-id="b0595-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b0595-149">Quando l'autorizzazione ha esito positivo, `context.Succeed` viene richiamato con il requisito soddisfatto come unico parametro.</span><span class="sxs-lookup"><span data-stu-id="b0595-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b0595-150">Usare un gestore per più requisiti</span><span class="sxs-lookup"><span data-stu-id="b0595-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b0595-151">Di seguito è riportato un esempio di una relazione uno-a-molti in cui un gestore di autorizzazioni è in grado di gestire tre diversi tipi di requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b0595-152">Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una proprietà che contiene requisiti non contrassegnati come riusciti.</span><span class="sxs-lookup"><span data-stu-id="b0595-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b0595-153">Per un `ReadPermission` requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b0595-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b0595-154">Nel caso di un `EditPermission` requisito o `DeletePermission` , deve essere un proprietario per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b0595-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b0595-155">Registrazione del gestore</span><span class="sxs-lookup"><span data-stu-id="b0595-155">Handler registration</span></span>

<span data-ttu-id="b0595-156">I gestori vengono registrati nella raccolta di servizi durante la configurazione.</span><span class="sxs-lookup"><span data-stu-id="b0595-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b0595-157">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="b0595-158">Il codice precedente viene registrato `MinimumAgeHandler` come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="b0595-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b0595-159">I gestori possono essere registrati usando una qualsiasi delle [durate dei servizi](xref:fundamentals/dependency-injection#service-lifetimes)predefinite.</span><span class="sxs-lookup"><span data-stu-id="b0595-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b0595-160">Che cosa deve restituire un gestore?</span><span class="sxs-lookup"><span data-stu-id="b0595-160">What should a handler return?</span></span>

<span data-ttu-id="b0595-161">Si noti che il `Handle` metodo nell' [esempio di gestore](#security-authorization-handler-example) non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="b0595-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b0595-162">Come viene indicato lo stato di esito positivo o negativo?</span><span class="sxs-lookup"><span data-stu-id="b0595-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b0595-163">Un gestore indica l'esito positivo chiamando `context.Succeed(IAuthorizationRequirement requirement)` , passando il requisito che è stato convalidato correttamente.</span><span class="sxs-lookup"><span data-stu-id="b0595-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b0595-164">Un gestore non deve gestire gli errori in modo generale, perché gli altri gestori per lo stesso requisito possono avere esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b0595-165">Per garantire un errore, anche se altri gestori di requisiti hanno esito positivo, chiamare `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="b0595-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b0595-166">Se un gestore chiama `context.Succeed` o `context.Fail` , tutti gli altri gestori vengono ancora chiamati.</span><span class="sxs-lookup"><span data-stu-id="b0595-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b0595-167">Questo consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha correttamente convalidato o non ha superato un requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b0595-168">Quando è impostato su `false` , la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) esegue il cortocircuito dell'esecuzione dei gestori quando `context.Fail` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="b0595-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b0595-169">`InvokeHandlersAfterFailure` il valore predefinito `true` è, nel qual caso vengono chiamati tutti i gestori.</span><span class="sxs-lookup"><span data-stu-id="b0595-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b0595-170">I gestori di autorizzazione vengono chiamati anche se l'autenticazione ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b0595-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b0595-171">Perché si vogliono usare più gestori per un requisito?</span><span class="sxs-lookup"><span data-stu-id="b0595-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b0595-172">Nei casi in cui si desidera che la valutazione sia basata su **o** , implementare più gestori per un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b0595-173">Ad esempio, Microsoft dispone di porte che si aprono solo con le schede chiave.</span><span class="sxs-lookup"><span data-stu-id="b0595-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b0595-174">Se si lascia la scheda chiave a casa, il receptionist stampa un adesivo temporaneo e apre lo sportello.</span><span class="sxs-lookup"><span data-stu-id="b0595-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b0595-175">In questo scenario si avrà un singolo requisito, *BuildingEntry*, ma più gestori, ognuno di essi esaminando un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b0595-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b0595-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b0595-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b0595-179">Assicurarsi che entrambi i gestori siano [registrati](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b0595-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b0595-180">Se uno dei due gestori ha esito positivo quando un criterio valuta `BuildingEntryRequirement` , la valutazione dei criteri ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b0595-181">Usare una funzione Func per soddisfare un criterio</span><span class="sxs-lookup"><span data-stu-id="b0595-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="b0595-182">In alcuni casi è possibile che l'evasione di un criterio sia semplice da esprimere nel codice.</span><span class="sxs-lookup"><span data-stu-id="b0595-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b0595-183">È possibile fornire un oggetto `Func<AuthorizationHandlerContext, bool>` quando si configurano i criteri con il `RequireAssertion` Generatore di criteri.</span><span class="sxs-lookup"><span data-stu-id="b0595-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b0595-184">Ad esempio, il precedente `BadgeEntryHandler` potrebbe essere riscritto come segue:</span><span class="sxs-lookup"><span data-stu-id="b0595-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="b0595-185">Accedere al contesto della richiesta MVC nei gestori</span><span class="sxs-lookup"><span data-stu-id="b0595-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="b0595-186">Il `HandleRequirementAsync` metodo implementato in un gestore autorizzazioni presenta due parametri: un oggetto `AuthorizationHandlerContext` e l'oggetto `TRequirement` che si sta gestendo.</span><span class="sxs-lookup"><span data-stu-id="b0595-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b0595-187">I Framework come MVC o SignalR possono aggiungere qualsiasi oggetto alla `Resource` Proprietà in `AuthorizationHandlerContext` per passare informazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b0595-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b0595-188">Quando si usa il routing degli endpoint, l'autorizzazione viene in genere gestita dal middleware di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b0595-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="b0595-189">In questo caso, la `Resource` proprietà è un'istanza di <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="b0595-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="b0595-190">L'endpoint può essere usato per eseguire il probe della risorsa sottostante a cui si sta eseguendo il routing.</span><span class="sxs-lookup"><span data-stu-id="b0595-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="b0595-191">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="b0595-192">L'endpoint non fornisce l'accesso all'oggetto corrente `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b0595-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="b0595-193">Quando si utilizza il routing dell'endpoint, utilizzare `IHttpContextAcessor` per accedere `HttpContext` all'interno di un gestore autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="b0595-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="b0595-194">Per altre informazioni, vedere [usare HttpContext da componenti personalizzati](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="b0595-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="b0595-195">Con il routing tradizionale o quando viene eseguita l'autorizzazione come parte del filtro di autorizzazione di MVC, il valore di `Resource` è un' <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> istanza di.</span><span class="sxs-lookup"><span data-stu-id="b0595-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="b0595-196">Questa proprietà consente di accedere a `HttpContext` , `RouteData` e a tutti gli altri elementi forniti da MVC e dalle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="b0595-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b0595-197">L'uso della `Resource` proprietà è specifico del Framework.</span><span class="sxs-lookup"><span data-stu-id="b0595-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b0595-198">L'uso delle informazioni nella `Resource` proprietà limita i criteri di autorizzazione a specifici Framework.</span><span class="sxs-lookup"><span data-stu-id="b0595-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b0595-199">È necessario eseguire il cast della `Resource` proprietà usando la `is` parola chiave e quindi verificare che il cast abbia avuto esito positivo per verificare che il codice non si arresti in modo anomalo con un `InvalidCastException` quando viene eseguito in altri Framework:</span><span class="sxs-lookup"><span data-stu-id="b0595-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="b0595-200">Richiedere l'autenticazione di tutti gli utenti a livello globale</span><span class="sxs-lookup"><span data-stu-id="b0595-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0595-201">Dietro le quinte, l'autorizzazione [basata sui ruoli](xref:security/authorization/roles) e l' [autorizzazione basata sulle attestazioni](xref:security/authorization/claims) utilizzano un requisito, un gestore requisiti e un criterio preconfigurato.</span><span class="sxs-lookup"><span data-stu-id="b0595-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b0595-202">Questi blocchi predefiniti supportano l'espressione delle valutazioni di autorizzazione nel codice.</span><span class="sxs-lookup"><span data-stu-id="b0595-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b0595-203">Il risultato è una struttura di autorizzazione verificabile, riutilizzabile e più ricca.</span><span class="sxs-lookup"><span data-stu-id="b0595-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b0595-204">Un criterio di autorizzazione è costituito da uno o più requisiti.</span><span class="sxs-lookup"><span data-stu-id="b0595-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b0595-205">Viene registrato come parte della configurazione del servizio di autorizzazione, nel `Startup.ConfigureServices` Metodo:</span><span class="sxs-lookup"><span data-stu-id="b0595-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="b0595-206">Nell'esempio precedente viene creato un criterio "AtLeast21".</span><span class="sxs-lookup"><span data-stu-id="b0595-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b0595-207">Ha un singolo requisito &mdash; che di età minima, fornito come parametro al requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b0595-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b0595-208">IAuthorizationService</span></span> 

<span data-ttu-id="b0595-209">Il servizio primario che determina se l'autorizzazione ha esito positivo è <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="b0595-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="b0595-210">Il codice precedente evidenzia i due metodi di [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span><span class="sxs-lookup"><span data-stu-id="b0595-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b0595-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> è un servizio marcatore senza metodi e il meccanismo per verificare se l'autorizzazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b0595-212">Ogni <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> è responsabile di verificare se sono soddisfatti i requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b0595-213">La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe è l'elemento usato dal gestore per contrassegnare se sono stati soddisfatti i requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b0595-214">Nel codice seguente viene illustrata l'implementazione predefinita del servizio di autorizzazione semplificata (e annotata con commenti):</span><span class="sxs-lookup"><span data-stu-id="b0595-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b0595-215">Il codice seguente illustra un tipico `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b0595-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b0595-216">Utilizzare <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> o `[Authorize(Policy = "Something")]` per l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b0595-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="b0595-217">Applicare criteri ai controller MVC</span><span class="sxs-lookup"><span data-stu-id="b0595-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="b0595-218">Se si usano le Razor pagine, vedere [applicare i criteri alle Razor pagine](#apply-policies-to-razor-pages) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="b0595-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b0595-219">I criteri vengono applicati ai controller usando l' `[Authorize]` attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="b0595-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0595-220">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="b0595-221">Applicare criteri alle Razor pagine</span><span class="sxs-lookup"><span data-stu-id="b0595-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="b0595-222">I criteri vengono applicati alle Razor pagine usando l' `[Authorize]` attributo con il nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="b0595-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b0595-223">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b0595-224">I criteri possono essere applicati anche alle Razor pagine utilizzando una [convenzione di autorizzazione](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="b0595-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b0595-225">Requisiti</span><span class="sxs-lookup"><span data-stu-id="b0595-225">Requirements</span></span>

<span data-ttu-id="b0595-226">Un requisito di autorizzazione è una raccolta di parametri dati che un criterio può utilizzare per valutare l'entità utente corrente.</span><span class="sxs-lookup"><span data-stu-id="b0595-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b0595-227">Nel criterio "AtLeast21", il requisito è un singolo parametro &mdash; la validità minima.</span><span class="sxs-lookup"><span data-stu-id="b0595-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b0595-228">Un requisito implementa [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), che è un'interfaccia marcatore vuota.</span><span class="sxs-lookup"><span data-stu-id="b0595-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b0595-229">Un requisito di validità minima con parametri può essere implementato come segue:</span><span class="sxs-lookup"><span data-stu-id="b0595-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b0595-230">Se un criterio di autorizzazione contiene più requisiti di autorizzazione, tutti i requisiti devono essere superati affinché la valutazione dei criteri abbia esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b0595-231">In altre parole, i requisiti di autorizzazione multipli aggiunti a un singolo criterio di autorizzazione vengono gestiti in base a **e** .</span><span class="sxs-lookup"><span data-stu-id="b0595-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b0595-232">Un requisito non deve avere dati o proprietà.</span><span class="sxs-lookup"><span data-stu-id="b0595-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b0595-233">Gestori autorizzazioni</span><span class="sxs-lookup"><span data-stu-id="b0595-233">Authorization handlers</span></span>

<span data-ttu-id="b0595-234">Un gestore di autorizzazione è responsabile della valutazione delle proprietà di un requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b0595-235">Il gestore di autorizzazione valuta i requisiti rispetto a un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) specificato per determinare se l'accesso è consentito.</span><span class="sxs-lookup"><span data-stu-id="b0595-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b0595-236">Un requisito può avere [più gestori](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="b0595-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b0595-237">Un gestore può ereditare [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), dove `TRequirement` è il requisito da gestire.</span><span class="sxs-lookup"><span data-stu-id="b0595-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b0595-238">In alternativa, un gestore può implementare [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) per gestire più di un tipo di requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b0595-239">Usare un gestore per un requisito</span><span class="sxs-lookup"><span data-stu-id="b0595-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b0595-240">Di seguito è riportato un esempio di una relazione uno-a-uno in cui un gestore età minima utilizza un singolo requisito:</span><span class="sxs-lookup"><span data-stu-id="b0595-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b0595-241">Il codice precedente determina se l'entità utente corrente ha una data di attestazione di nascita che è stata rilasciata da un'autorità emittente nota ed attendibile.</span><span class="sxs-lookup"><span data-stu-id="b0595-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b0595-242">L'autorizzazione non può verificarsi quando l'attestazione non è presente, nel qual caso viene restituita un'attività completata.</span><span class="sxs-lookup"><span data-stu-id="b0595-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b0595-243">Quando è presente un'attestazione, viene calcolata l'età dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b0595-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b0595-244">Se l'utente soddisfa la validità minima definita dal requisito, l'autorizzazione viene ritenuta corretta.</span><span class="sxs-lookup"><span data-stu-id="b0595-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b0595-245">Quando l'autorizzazione ha esito positivo, `context.Succeed` viene richiamato con il requisito soddisfatto come unico parametro.</span><span class="sxs-lookup"><span data-stu-id="b0595-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b0595-246">Usare un gestore per più requisiti</span><span class="sxs-lookup"><span data-stu-id="b0595-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b0595-247">Di seguito è riportato un esempio di una relazione uno-a-molti in cui un gestore di autorizzazioni è in grado di gestire tre diversi tipi di requisiti:</span><span class="sxs-lookup"><span data-stu-id="b0595-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b0595-248">Il codice precedente attraversa [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; una proprietà che contiene requisiti non contrassegnati come riusciti.</span><span class="sxs-lookup"><span data-stu-id="b0595-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b0595-249">Per un `ReadPermission` requisito, l'utente deve essere un proprietario o uno sponsor per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b0595-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b0595-250">Nel caso di un `EditPermission` requisito o `DeletePermission` , deve essere un proprietario per accedere alla risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="b0595-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b0595-251">Registrazione del gestore</span><span class="sxs-lookup"><span data-stu-id="b0595-251">Handler registration</span></span>

<span data-ttu-id="b0595-252">I gestori vengono registrati nella raccolta di servizi durante la configurazione.</span><span class="sxs-lookup"><span data-stu-id="b0595-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b0595-253">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b0595-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="b0595-254">Il codice precedente viene registrato `MinimumAgeHandler` come singleton richiamando `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="b0595-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b0595-255">I gestori possono essere registrati usando una qualsiasi delle [durate dei servizi](xref:fundamentals/dependency-injection#service-lifetimes)predefinite.</span><span class="sxs-lookup"><span data-stu-id="b0595-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b0595-256">Che cosa deve restituire un gestore?</span><span class="sxs-lookup"><span data-stu-id="b0595-256">What should a handler return?</span></span>

<span data-ttu-id="b0595-257">Si noti che il `Handle` metodo nell' [esempio di gestore](#security-authorization-handler-example) non restituisce alcun valore.</span><span class="sxs-lookup"><span data-stu-id="b0595-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b0595-258">Come viene indicato lo stato di esito positivo o negativo?</span><span class="sxs-lookup"><span data-stu-id="b0595-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b0595-259">Un gestore indica l'esito positivo chiamando `context.Succeed(IAuthorizationRequirement requirement)` , passando il requisito che è stato convalidato correttamente.</span><span class="sxs-lookup"><span data-stu-id="b0595-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b0595-260">Un gestore non deve gestire gli errori in modo generale, perché gli altri gestori per lo stesso requisito possono avere esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b0595-261">Per garantire un errore, anche se altri gestori di requisiti hanno esito positivo, chiamare `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="b0595-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b0595-262">Se un gestore chiama `context.Succeed` o `context.Fail` , tutti gli altri gestori vengono ancora chiamati.</span><span class="sxs-lookup"><span data-stu-id="b0595-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b0595-263">Questo consente ai requisiti di produrre effetti collaterali, ad esempio la registrazione, che si verifica anche se un altro gestore ha correttamente convalidato o non ha superato un requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b0595-264">Quando è impostato su `false` , la proprietà [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) esegue il cortocircuito dell'esecuzione dei gestori quando `context.Fail` viene chiamato il metodo.</span><span class="sxs-lookup"><span data-stu-id="b0595-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b0595-265">`InvokeHandlersAfterFailure` il valore predefinito `true` è, nel qual caso vengono chiamati tutti i gestori.</span><span class="sxs-lookup"><span data-stu-id="b0595-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b0595-266">I gestori di autorizzazione vengono chiamati anche se l'autenticazione ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b0595-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b0595-267">Perché si vogliono usare più gestori per un requisito?</span><span class="sxs-lookup"><span data-stu-id="b0595-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b0595-268">Nei casi in cui si desidera che la valutazione sia basata su **o** , implementare più gestori per un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b0595-269">Ad esempio, Microsoft dispone di porte che si aprono solo con le schede chiave.</span><span class="sxs-lookup"><span data-stu-id="b0595-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b0595-270">Se si lascia la scheda chiave a casa, il receptionist stampa un adesivo temporaneo e apre lo sportello.</span><span class="sxs-lookup"><span data-stu-id="b0595-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b0595-271">In questo scenario si avrà un singolo requisito, *BuildingEntry*, ma più gestori, ognuno di essi esaminando un singolo requisito.</span><span class="sxs-lookup"><span data-stu-id="b0595-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b0595-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b0595-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b0595-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b0595-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b0595-275">Assicurarsi che entrambi i gestori siano [registrati](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="b0595-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b0595-276">Se uno dei due gestori ha esito positivo quando un criterio valuta `BuildingEntryRequirement` , la valutazione dei criteri ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="b0595-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b0595-277">Usare una funzione Func per soddisfare un criterio</span><span class="sxs-lookup"><span data-stu-id="b0595-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="b0595-278">In alcuni casi è possibile che l'evasione di un criterio sia semplice da esprimere nel codice.</span><span class="sxs-lookup"><span data-stu-id="b0595-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b0595-279">È possibile fornire un oggetto `Func<AuthorizationHandlerContext, bool>` quando si configurano i criteri con il `RequireAssertion` Generatore di criteri.</span><span class="sxs-lookup"><span data-stu-id="b0595-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b0595-280">Ad esempio, il precedente `BadgeEntryHandler` potrebbe essere riscritto come segue:</span><span class="sxs-lookup"><span data-stu-id="b0595-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="b0595-281">Accedere al contesto della richiesta MVC nei gestori</span><span class="sxs-lookup"><span data-stu-id="b0595-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="b0595-282">Il `HandleRequirementAsync` metodo implementato in un gestore autorizzazioni presenta due parametri: un oggetto `AuthorizationHandlerContext` e l'oggetto `TRequirement` che si sta gestendo.</span><span class="sxs-lookup"><span data-stu-id="b0595-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b0595-283">I Framework come MVC o SignalR possono aggiungere qualsiasi oggetto alla `Resource` Proprietà in `AuthorizationHandlerContext` per passare informazioni aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b0595-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b0595-284">Ad esempio, MVC passa un'istanza di [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) nella `Resource` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="b0595-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b0595-285">Questa proprietà consente di accedere a `HttpContext` , `RouteData` e a tutti gli altri elementi forniti da MVC e dalle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="b0595-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b0595-286">L'uso della `Resource` proprietà è specifico del Framework.</span><span class="sxs-lookup"><span data-stu-id="b0595-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b0595-287">L'uso delle informazioni nella `Resource` proprietà limita i criteri di autorizzazione a specifici Framework.</span><span class="sxs-lookup"><span data-stu-id="b0595-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b0595-288">È necessario eseguire il cast della `Resource` proprietà usando la `is` parola chiave e quindi verificare che il cast abbia avuto esito positivo per verificare che il codice non si arresti in modo anomalo con un `InvalidCastException` quando viene eseguito in altri Framework:</span><span class="sxs-lookup"><span data-stu-id="b0595-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
