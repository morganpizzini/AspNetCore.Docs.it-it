---
title: Provider di criteri di autorizzazione personalizzati in ASP.NET Core
author: mjrousos
description: Informazioni su come usare un IAuthorizationPolicyProvider personalizzato in un'app ASP.NET Core per generare dinamicamente i criteri di autorizzazione.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2d231440847270b3b2fe47fbe29359f494900292
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635203"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="2a547-103">Provider di criteri di autorizzazione personalizzati che usano IAuthorizationPolicyProvider in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a547-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="2a547-104">Di [Mike risveglia](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2a547-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="2a547-105">In genere, quando si utilizza l' [autorizzazione basata su criteri](xref:security/authorization/policies), i criteri vengono registrati chiamando `AuthorizationOptions.AddPolicy` come parte della configurazione del servizio di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2a547-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="2a547-106">In alcuni scenari potrebbe non essere possibile (o auspicabile) registrare tutti i criteri di autorizzazione in questo modo.</span><span class="sxs-lookup"><span data-stu-id="2a547-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="2a547-107">In questi casi, è possibile [usare un oggetto `IAuthorizationPolicyProvider` personalizzato](#ci) per controllare come vengono forniti i criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2a547-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="2a547-108">Esempi di scenari in cui un [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) personalizzato può essere utile includono:</span><span class="sxs-lookup"><span data-stu-id="2a547-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="2a547-109">Uso di un servizio esterno per fornire la valutazione dei criteri.</span><span class="sxs-lookup"><span data-stu-id="2a547-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="2a547-110">Usando un'ampia gamma di criteri (ad esempio, per diversi numeri di stanza o età), non ha senso aggiungere ogni singolo criterio di autorizzazione con una `AuthorizationOptions.AddPolicy` chiamata.</span><span class="sxs-lookup"><span data-stu-id="2a547-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="2a547-111">Creazione di criteri in fase di esecuzione in base alle informazioni in un'origine dati esterna, ad esempio un database, o determinazione dinamica dei requisiti di autorizzazione tramite un altro meccanismo.</span><span class="sxs-lookup"><span data-stu-id="2a547-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="2a547-112">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) dal [repository GitHub AspNetCore](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="2a547-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="2a547-113">Scaricare il file ZIP del repository DotNet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="2a547-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="2a547-114">Decomprimere il file.</span><span class="sxs-lookup"><span data-stu-id="2a547-114">Unzip the file.</span></span> <span data-ttu-id="2a547-115">Passare alla cartella di progetto *SRC/Security/Samples/CustomPolicyProvider* .</span><span class="sxs-lookup"><span data-stu-id="2a547-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="2a547-116">Personalizzare il recupero dei criteri</span><span class="sxs-lookup"><span data-stu-id="2a547-116">Customize policy retrieval</span></span>

<span data-ttu-id="2a547-117">Le app ASP.NET Core usano un'implementazione dell' `IAuthorizationPolicyProvider` interfaccia per recuperare i criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2a547-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="2a547-118">Per impostazione predefinita, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) è registrato e usato.</span><span class="sxs-lookup"><span data-stu-id="2a547-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="2a547-119">`DefaultAuthorizationPolicyProvider` Restituisce i criteri dall'oggetto `AuthorizationOptions` fornito in una `IServiceCollection.AddAuthorization` chiamata a.</span><span class="sxs-lookup"><span data-stu-id="2a547-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="2a547-120">Personalizzare questo comportamento registrando un'implementazione diversa `IAuthorizationPolicyProvider` nel contenitore di inserimento delle [dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="2a547-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="2a547-121">L' `IAuthorizationPolicyProvider` interfaccia contiene tre API:</span><span class="sxs-lookup"><span data-stu-id="2a547-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="2a547-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) restituisce un criterio di autorizzazione per un nome specificato.</span><span class="sxs-lookup"><span data-stu-id="2a547-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="2a547-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) restituisce i criteri di autorizzazione predefiniti (il criterio utilizzato per `[Authorize]` gli attributi senza un criterio specificato).</span><span class="sxs-lookup"><span data-stu-id="2a547-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="2a547-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) restituisce i criteri di autorizzazione di fallback (il criterio utilizzato dal middleware di autorizzazione quando non è specificato alcun criterio).</span><span class="sxs-lookup"><span data-stu-id="2a547-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="2a547-125">Implementando queste API, è possibile personalizzare il modo in cui vengono forniti i criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="2a547-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="2a547-126">Esempio di attributo di autorizzazione con parametri</span><span class="sxs-lookup"><span data-stu-id="2a547-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="2a547-127">Uno scenario `IAuthorizationPolicyProvider` in cui è utile è l'abilitazione `[Authorize]` di attributi personalizzati i cui requisiti dipendono da un parametro.</span><span class="sxs-lookup"><span data-stu-id="2a547-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="2a547-128">Nella documentazione [sull'autorizzazione basata su criteri](xref:security/authorization/policies) , ad esempio, è stato usato un criterio basato sull'età ("AtLeast21") come esempio.</span><span class="sxs-lookup"><span data-stu-id="2a547-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="2a547-129">Se le azioni del controller diverse in un'app devono essere rese disponibili agli utenti con età *diverse* , potrebbe essere utile avere molti criteri basati sull'età diversi.</span><span class="sxs-lookup"><span data-stu-id="2a547-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="2a547-130">Anziché registrare tutti i diversi criteri basati sull'età necessari per l'applicazione `AuthorizationOptions` , è possibile generare i criteri in modo dinamico con un oggetto personalizzato `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="2a547-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="2a547-131">Per semplificare l'uso dei criteri, è possibile annotare le azioni con l'attributo di autorizzazione personalizzato, ad esempio `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="2a547-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="2a547-132">Attributi di autorizzazione personalizzati</span><span class="sxs-lookup"><span data-stu-id="2a547-132">Custom Authorization attributes</span></span>

<span data-ttu-id="2a547-133">I criteri di autorizzazione sono identificati dai rispettivi nomi.</span><span class="sxs-lookup"><span data-stu-id="2a547-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="2a547-134">L'oggetto personalizzato `MinimumAgeAuthorizeAttribute` descritto in precedenza deve eseguire il mapping degli argomenti in una stringa che può essere utilizzata per recuperare i criteri di autorizzazione corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="2a547-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="2a547-135">È possibile eseguire questa operazione derivando da `AuthorizeAttribute` e rendendo la proprietà `Age` incapsulata `AuthorizeAttribute.Policy` .</span><span class="sxs-lookup"><span data-stu-id="2a547-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="2a547-136">Questo tipo di attributo ha una `Policy` stringa basata sul prefisso hardcoded ( `"MinimumAge"` ) e un numero intero passato tramite il costruttore.</span><span class="sxs-lookup"><span data-stu-id="2a547-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="2a547-137">È possibile applicarlo alle azioni in modo analogo ad altri `Authorize` attributi, ad eccezione del fatto che accetta un Integer come parametro.</span><span class="sxs-lookup"><span data-stu-id="2a547-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="2a547-138">IAuthorizationPolicyProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="2a547-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="2a547-139">Il personalizzato `MinimumAgeAuthorizeAttribute` rende più semplice richiedere i criteri di autorizzazione per qualsiasi età minima desiderata.</span><span class="sxs-lookup"><span data-stu-id="2a547-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="2a547-140">Il prossimo problema da risolvere è garantire che i criteri di autorizzazione siano disponibili per tutte le epoche diverse.</span><span class="sxs-lookup"><span data-stu-id="2a547-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="2a547-141">Questa è la posizione in cui `IAuthorizationPolicyProvider` è utile.</span><span class="sxs-lookup"><span data-stu-id="2a547-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="2a547-142">Quando `MinimumAgeAuthorizationAttribute` si usa, i nomi dei criteri di autorizzazione seguiranno il modello `"MinimumAge" + Age` , pertanto l'oggetto personalizzato `IAuthorizationPolicyProvider` deve generare i criteri di autorizzazione per:</span><span class="sxs-lookup"><span data-stu-id="2a547-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="2a547-143">Analisi dell'età dal nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="2a547-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="2a547-144">Utilizzo `AuthorizationPolicyBuilder` di per creare un nuovo `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="2a547-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="2a547-145">In questo e negli esempi seguenti si presuppone che l'utente venga autenticato tramite un cookie .</span><span class="sxs-lookup"><span data-stu-id="2a547-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="2a547-146">Il `AuthorizationPolicyBuilder` deve essere costruito con almeno un nome dello schema di autorizzazione o avere sempre esito positivo.</span><span class="sxs-lookup"><span data-stu-id="2a547-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="2a547-147">In caso contrario, non sono disponibili informazioni su come fornire un problema all'utente e verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="2a547-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="2a547-148">Aggiunta di requisiti ai criteri in base all'età con `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="2a547-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="2a547-149">In altri scenari, è possibile usare `RequireClaim` `RequireRole` invece, o `RequireUserName` .</span><span class="sxs-lookup"><span data-stu-id="2a547-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="2a547-150">Provider di criteri di autorizzazione multipli</span><span class="sxs-lookup"><span data-stu-id="2a547-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="2a547-151">Quando si utilizzano `IAuthorizationPolicyProvider` implementazioni personalizzate, tenere presente che ASP.NET Core utilizza solo un'istanza di `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="2a547-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="2a547-152">Se un provider personalizzato non è in grado di fornire i criteri di autorizzazione per tutti i nomi di criteri che verranno usati, deve rinviare a un provider di backup.</span><span class="sxs-lookup"><span data-stu-id="2a547-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="2a547-153">Si consideri, ad esempio, un'applicazione che richiede criteri di validità personalizzati e il recupero di criteri più tradizionali basati sui ruoli.</span><span class="sxs-lookup"><span data-stu-id="2a547-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="2a547-154">Tale app potrebbe usare un provider di criteri di autorizzazione personalizzato che:</span><span class="sxs-lookup"><span data-stu-id="2a547-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="2a547-155">Tenta di analizzare i nomi dei criteri.</span><span class="sxs-lookup"><span data-stu-id="2a547-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="2a547-156">Chiama un provider di criteri diverso (ad esempio `DefaultAuthorizationPolicyProvider` ) se il nome dei criteri non contiene un'età.</span><span class="sxs-lookup"><span data-stu-id="2a547-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="2a547-157">L'implementazione di esempio `IAuthorizationPolicyProvider` illustrata in precedenza può essere aggiornata per usare il `DefaultAuthorizationPolicyProvider` creando un provider di criteri di backup nel relativo costruttore (da usare nel caso in cui il nome dei criteri non corrisponda al modello previsto di "minime" + Age).</span><span class="sxs-lookup"><span data-stu-id="2a547-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="2a547-158">Quindi, il `GetPolicyAsync` metodo può essere aggiornato in modo da usare `BackupPolicyProvider` anziché restituire null:</span><span class="sxs-lookup"><span data-stu-id="2a547-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="2a547-159">Criteri predefiniti</span><span class="sxs-lookup"><span data-stu-id="2a547-159">Default policy</span></span>

<span data-ttu-id="2a547-160">Oltre a fornire i criteri di autorizzazione denominati, è necessario implementare un oggetto personalizzato `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` per fornire un criterio di autorizzazione per `[Authorize]` gli attributi senza specificare un nome di criterio.</span><span class="sxs-lookup"><span data-stu-id="2a547-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="2a547-161">In molti casi, questo attributo di autorizzazione richiede solo un utente autenticato, pertanto è possibile effettuare i criteri necessari con una chiamata a `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="2a547-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="2a547-162">Come per tutti gli aspetti di un oggetto personalizzato `IAuthorizationPolicyProvider` , è possibile personalizzarlo in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="2a547-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="2a547-163">In alcuni casi, può essere utile recuperare i criteri predefiniti da un fallback `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="2a547-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="2a547-164">Criteri di fallback</span><span class="sxs-lookup"><span data-stu-id="2a547-164">Fallback policy</span></span>

<span data-ttu-id="2a547-165">Un oggetto personalizzato `IAuthorizationPolicyProvider` può facoltativamente implementare `GetFallbackPolicyAsync` per fornire un criterio usato durante la [combinazione di criteri](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando non vengono specificati criteri.</span><span class="sxs-lookup"><span data-stu-id="2a547-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="2a547-166">Se `GetFallbackPolicyAsync` restituisce un criterio non null, i criteri restituiti vengono utilizzati dal middleware di autorizzazione quando non vengono specificati criteri per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="2a547-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="2a547-167">Se non è richiesto alcun criterio di fallback, il provider può restituire `null` o rinviare al provider di fallback:</span><span class="sxs-lookup"><span data-stu-id="2a547-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="2a547-168">Usare un IAuthorizationPolicyProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="2a547-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="2a547-169">Per usare i criteri personalizzati da un `IAuthorizationPolicyProvider` , è ***necessario***:</span><span class="sxs-lookup"><span data-stu-id="2a547-169">To use custom policies from an `IAuthorizationPolicyProvider`, you ***must***:</span></span>

* <span data-ttu-id="2a547-170">Registrare i `AuthorizationHandler` tipi appropriati con l'inserimento di dipendenze (descritto in [autorizzazione basata su criteri](xref:security/authorization/policies#authorization-handlers)), come per tutti gli scenari di autorizzazione basata su criteri.</span><span class="sxs-lookup"><span data-stu-id="2a547-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="2a547-171">Registrare il `IAuthorizationPolicyProvider` tipo personalizzato nella raccolta di servizi di inserimento delle dipendenze dell'app in `Startup.ConfigureServices` per sostituire il provider di criteri predefinito.</span><span class="sxs-lookup"><span data-stu-id="2a547-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="2a547-172">Un esempio completo personalizzato `IAuthorizationPolicyProvider` è disponibile nel [repository GitHub DotNet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="2a547-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
