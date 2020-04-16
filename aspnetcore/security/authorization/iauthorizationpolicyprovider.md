---
title: Provider di criteri di autorizzazione personalizzati in ASP.NET CoreCustom Authorization Policy Providers in ASP.NET Core
author: mjrousos
description: Informazioni su come usare un oggetto Personalizzato IAuthorizationPolicyProvider in un'app ASP.NET Core per generare dinamicamente i criteri di autorizzazione.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440922"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="fded9-103">Provider di criteri di autorizzazione personalizzati che utilizzano IAuthorizationPolicyProvider in ASP.NET CoreCustom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fded9-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="fded9-104">Di [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="fded9-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="fded9-105">In genere quando si utilizza l'autorizzazione `AuthorizationOptions.AddPolicy` [basata su criteri](xref:security/authorization/policies), i criteri vengono registrati chiamando come parte della configurazione del servizio di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="fded9-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="fded9-106">In alcuni scenari, potrebbe non essere possibile (o auspicabile) registrare tutti i criteri di autorizzazione in questo modo.</span><span class="sxs-lookup"><span data-stu-id="fded9-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="fded9-107">In questi casi, è `IAuthorizationPolicyProvider` possibile utilizzare un'usanza per controllare la modalità di fornitura dei criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="fded9-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="fded9-108">Esempi di scenari in cui un [oggetto personalizzato IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) può essere utile includono:Examples of scenarios where a custom IAuthorizationPolicyProvider may be useful include:</span><span class="sxs-lookup"><span data-stu-id="fded9-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="fded9-109">Utilizzo di un servizio esterno per fornire la valutazione dei criteri.</span><span class="sxs-lookup"><span data-stu-id="fded9-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="fded9-110">Utilizzando un'ampia gamma di criteri (ad esempio, per diversi numeri di stanza o età), `AuthorizationOptions.AddPolicy` quindi non ha senso aggiungere ogni singolo criterio di autorizzazione con una chiamata.</span><span class="sxs-lookup"><span data-stu-id="fded9-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="fded9-111">Creazione di criteri in fase di esecuzione in base alle informazioni in un'origine dati esterna (ad esempio un database) o determinazione dinamica dei requisiti di autorizzazione tramite un altro meccanismo.</span><span class="sxs-lookup"><span data-stu-id="fded9-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="fded9-112">[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) dal repository [AspNetCore GitHub](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="fded9-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="fded9-113">Scaricare il file zip del repository dotnet/AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="fded9-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="fded9-114">Decomprimere il file.</span><span class="sxs-lookup"><span data-stu-id="fded9-114">Unzip the file.</span></span> <span data-ttu-id="fded9-115">Passare alla cartella del progetto *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="fded9-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="fded9-116">Personalizzare il recupero dei criteri</span><span class="sxs-lookup"><span data-stu-id="fded9-116">Customize policy retrieval</span></span>

<span data-ttu-id="fded9-117">ASP.NET le app principali `IAuthorizationPolicyProvider` usano un'implementazione dell'interfaccia per recuperare i criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="fded9-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="fded9-118">Per impostazione predefinita, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) viene registrato e utilizzato.</span><span class="sxs-lookup"><span data-stu-id="fded9-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="fded9-119">`DefaultAuthorizationPolicyProvider`restituisce le `AuthorizationOptions` politiche `IServiceCollection.AddAuthorization` dal fornito in una chiamata.</span><span class="sxs-lookup"><span data-stu-id="fded9-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="fded9-120">Personalizza questo comportamento registrando un'implementazione diversa `IAuthorizationPolicyProvider` nel contenitore di inserimento delle [dipendenze](xref:fundamentals/dependency-injection) dell'app.</span><span class="sxs-lookup"><span data-stu-id="fded9-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="fded9-121">L'interfaccia `IAuthorizationPolicyProvider` contiene tre API:The interface contains three APIs:</span><span class="sxs-lookup"><span data-stu-id="fded9-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="fded9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) restituisce un criterio di autorizzazione per un nome specificato.</span><span class="sxs-lookup"><span data-stu-id="fded9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="fded9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) restituisce i criteri di `[Authorize]` autorizzazione predefiniti (i criteri utilizzati per gli attributi senza un criterio specificato).</span><span class="sxs-lookup"><span data-stu-id="fded9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="fded9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) restituisce i criteri di autorizzazione di fallback (i criteri utilizzati dal middleware di autorizzazione quando non viene specificato alcun criterio).</span><span class="sxs-lookup"><span data-stu-id="fded9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="fded9-125">Implementando queste API, è possibile personalizzare la modalità di presentazione dei criteri di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="fded9-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="fded9-126">Esempio di attributo di autorizzazione con parametriParameterized authorize attribute example</span><span class="sxs-lookup"><span data-stu-id="fded9-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="fded9-127">Uno scenario `IAuthorizationPolicyProvider` in cui `[Authorize]` è utile è l'abilitazione di attributi personalizzati i cui requisiti dipendono da un parametro.</span><span class="sxs-lookup"><span data-stu-id="fded9-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="fded9-128">Ad esempio, nella documentazione relativa [all'autorizzazione basata su criteri,](xref:security/authorization/policies) un criterio basato sull'età ("AtLeast21") è stato utilizzato come esempio.</span><span class="sxs-lookup"><span data-stu-id="fded9-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="fded9-129">Se diverse azioni del controller in un'app devono essere rese disponibili per utenti di età *diverse,* potrebbe essere utile disporre di molti criteri basati sull'età diversi.</span><span class="sxs-lookup"><span data-stu-id="fded9-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="fded9-130">Anziché registrare tutti i diversi criteri basati `AuthorizationOptions`sull'età necessari all'applicazione `IAuthorizationPolicyProvider`in , è possibile generare i criteri in modo dinamico con un oggetto personalizzato .</span><span class="sxs-lookup"><span data-stu-id="fded9-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="fded9-131">Per semplificare l'utilizzo dei criteri, è possibile `[MinimumAgeAuthorize(20)]`annotare le azioni con l'attributo di autorizzazione personalizzato come .</span><span class="sxs-lookup"><span data-stu-id="fded9-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="fded9-132">Attributi di autorizzazione personalizzati</span><span class="sxs-lookup"><span data-stu-id="fded9-132">Custom Authorization attributes</span></span>

<span data-ttu-id="fded9-133">I criteri di autorizzazione sono identificati dai relativi nomi.</span><span class="sxs-lookup"><span data-stu-id="fded9-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="fded9-134">La `MinimumAgeAuthorizeAttribute` personalizzazione descritta in precedenza deve eseguire il mapping degli argomenti in una stringa che può essere utilizzata per recuperare i criteri di autorizzazione corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="fded9-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="fded9-135">È possibile eseguire questa `AuthorizeAttribute` operazione derivando da e facendo in modo che la proprietà esetrai il `Age` wrapping della `AuthorizeAttribute.Policy` proprietà.</span><span class="sxs-lookup"><span data-stu-id="fded9-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="fded9-136">Questo tipo di `Policy` attributo ha una stringa`"MinimumAge"`basata sul prefisso hardcoded ( ) e un numero intero passato tramite il costruttore.</span><span class="sxs-lookup"><span data-stu-id="fded9-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="fded9-137">È possibile applicarlo alle azioni nello `Authorize` stesso modo di altri attributi, ad eccezione del fatto che accetta un numero intero come parametro.</span><span class="sxs-lookup"><span data-stu-id="fded9-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="fded9-138">IAuthorizationPolicyProvider personalizzato</span><span class="sxs-lookup"><span data-stu-id="fded9-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="fded9-139">Il `MinimumAgeAuthorizeAttribute` costume di misura semplifica la richiesta di criteri di autorizzazione per qualsiasi età minima desiderata.</span><span class="sxs-lookup"><span data-stu-id="fded9-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="fded9-140">Il problema successivo da risolvere è assicurarsi che i criteri di autorizzazione siano disponibili per tutte queste diverse età.</span><span class="sxs-lookup"><span data-stu-id="fded9-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="fded9-141">Questo è `IAuthorizationPolicyProvider` dove un è utile.</span><span class="sxs-lookup"><span data-stu-id="fded9-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="fded9-142">Quando `MinimumAgeAuthorizationAttribute`si utilizza , i nomi `"MinimumAge" + Age`dei criteri `IAuthorizationPolicyProvider` di autorizzazione seguiranno il modello , pertanto l'impostazione personalizzata deve generare criteri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="fded9-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="fded9-143">Analisi dell'età dal nome del criterio.</span><span class="sxs-lookup"><span data-stu-id="fded9-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="fded9-144">Utilizzo `AuthorizationPolicyBuilder` per creare un nuovo`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="fded9-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="fded9-145">In questo e in questi esempi si presume che l'utente viene autenticato tramite un cookie.</span><span class="sxs-lookup"><span data-stu-id="fded9-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="fded9-146">L'oggetto `AuthorizationPolicyBuilder` deve essere costruito con almeno un nome di schema di autorizzazione o avere sempre esito positivo.</span><span class="sxs-lookup"><span data-stu-id="fded9-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="fded9-147">In caso contrario, non sono disponibili informazioni su come fornire una richiesta di verifica all'utente e verrà generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="fded9-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="fded9-148">Aggiunta di requisiti alla politica `AuthorizationPolicyBuilder.AddRequirements`in base all'età con .</span><span class="sxs-lookup"><span data-stu-id="fded9-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="fded9-149">In altri scenari, `RequireClaim`è `RequireRole`possibile `RequireUserName` utilizzare , , o invece.</span><span class="sxs-lookup"><span data-stu-id="fded9-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="fded9-150">Più provider di criteri di autorizzazioneMultiple authorization policy providers</span><span class="sxs-lookup"><span data-stu-id="fded9-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="fded9-151">Quando si `IAuthorizationPolicyProvider` utilizzano implementazioni personalizzate, tenere presente `IAuthorizationPolicyProvider`che ASP.NET Core utilizza solo un'istanza di .</span><span class="sxs-lookup"><span data-stu-id="fded9-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="fded9-152">Se un provider personalizzato non è in grado di fornire criteri di autorizzazione per tutti i nomi di criteri che verranno utilizzati, deve rinviare a un provider di backup.</span><span class="sxs-lookup"><span data-stu-id="fded9-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="fded9-153">Si consideri, ad esempio, un'applicazione che richiede sia criteri di età personalizzati che un recupero dei criteri basato sui ruoli più tradizionale.</span><span class="sxs-lookup"><span data-stu-id="fded9-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="fded9-154">Un'app di questo tipo potrebbe usare un provider di criteri di autorizzazione personalizzato che:Such an app could use a custom authorization policy provider that:</span><span class="sxs-lookup"><span data-stu-id="fded9-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="fded9-155">Tenta di analizzare i nomi dei criteri.</span><span class="sxs-lookup"><span data-stu-id="fded9-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="fded9-156">Chiama un provider di `DefaultAuthorizationPolicyProvider`criteri diverso (ad esempio ) se il nome del criterio non contiene un'età.</span><span class="sxs-lookup"><span data-stu-id="fded9-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="fded9-157">L'implementazione di esempio `IAuthorizationPolicyProvider` illustrata `DefaultAuthorizationPolicyProvider` in precedenza può essere aggiornata per usare il creando un provider di criteri di backup nel costruttore (da utilizzare nel caso in cui il nome del criterio non corrisponda al modello previsto di 'MinimumAge' e l'età).</span><span class="sxs-lookup"><span data-stu-id="fded9-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="fded9-158">Quindi, `GetPolicyAsync` il metodo può essere `BackupPolicyProvider` aggiornato per utilizzare il invece di restituire null:Then, the method can be updated to use the instead of returning null:</span><span class="sxs-lookup"><span data-stu-id="fded9-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="fded9-159">Criteri predefiniti</span><span class="sxs-lookup"><span data-stu-id="fded9-159">Default policy</span></span>

<span data-ttu-id="fded9-160">Oltre a fornire criteri di `IAuthorizationPolicyProvider` autorizzazione denominati, è necessario implementare `GetDefaultPolicyAsync` un'autorizzazione per fornire criteri di autorizzazione per `[Authorize]` gli attributi senza un nome di criterio specificato.</span><span class="sxs-lookup"><span data-stu-id="fded9-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="fded9-161">In molti casi, questo attributo di autorizzazione richiede solo un utente autenticato, pertanto è possibile effettuare i criteri necessari con una chiamata a `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="fded9-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="fded9-162">Come per tutti gli `IAuthorizationPolicyProvider`aspetti di un oggetto custom, è possibile personalizzarlo in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="fded9-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="fded9-163">In alcuni casi, può essere opportuno recuperare `IAuthorizationPolicyProvider`il criterio predefinito da un fallback.</span><span class="sxs-lookup"><span data-stu-id="fded9-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="fded9-164">Criteri di fallback</span><span class="sxs-lookup"><span data-stu-id="fded9-164">Fallback policy</span></span>

<span data-ttu-id="fded9-165">Un'attività `IAuthorizationPolicyProvider` personalizzata `GetFallbackPolicyAsync` può facoltativamente implementare per fornire un criterio che viene utilizzato quando [si combinano i criteri](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando non viene specificato alcun criterio.</span><span class="sxs-lookup"><span data-stu-id="fded9-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="fded9-166">Se `GetFallbackPolicyAsync` restituisce un criterio non null, il criterio restituito viene utilizzato dal middleware di autorizzazione quando non vengono specificati criteri per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="fded9-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="fded9-167">Se non sono necessari criteri di `null` fallback, il provider può restituire o rinviare al provider di fallback:If no fallback policy is required, the provider can return or defer to the fallback provider:</span><span class="sxs-lookup"><span data-stu-id="fded9-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="fded9-168">Usare un oggetto personalizzato IAuthorizationPolicyProviderUse a custom IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="fded9-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="fded9-169">Per utilizzare criteri `IAuthorizationPolicyProvider`personalizzati da un oggetto , è necessario:</span><span class="sxs-lookup"><span data-stu-id="fded9-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="fded9-170">Registrare `AuthorizationHandler` i tipi appropriati con inserimento delle dipendenze (descritto nell'autorizzazione [basata su criteri](xref:security/authorization/policies#authorization-handlers)), come con tutti gli scenari di autorizzazione basata su criteri.</span><span class="sxs-lookup"><span data-stu-id="fded9-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="fded9-171">Registrare `IAuthorizationPolicyProvider` il tipo personalizzato nella raccolta di servizi `Startup.ConfigureServices`di inserimento delle dipendenze dell'app (in ) per sostituire il provider di criteri predefinito.</span><span class="sxs-lookup"><span data-stu-id="fded9-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="fded9-172">Un esempio `IAuthorizationPolicyProvider` personalizzato completo è disponibile nel [repository dotnet/aspnetcore GitHub](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="fded9-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
