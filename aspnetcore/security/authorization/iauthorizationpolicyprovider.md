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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Provider di criteri di autorizzazione personalizzati che utilizzano IAuthorizationPolicyProvider in ASP.NET CoreCustom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core 

Di [Mike Rousos](https://github.com/mjrousos)

In genere quando si utilizza l'autorizzazione `AuthorizationOptions.AddPolicy` [basata su criteri](xref:security/authorization/policies), i criteri vengono registrati chiamando come parte della configurazione del servizio di autorizzazione. In alcuni scenari, potrebbe non essere possibile (o auspicabile) registrare tutti i criteri di autorizzazione in questo modo. In questi casi, è `IAuthorizationPolicyProvider` possibile utilizzare un'usanza per controllare la modalità di fornitura dei criteri di autorizzazione.

Esempi di scenari in cui un [oggetto personalizzato IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) può essere utile includono:Examples of scenarios where a custom IAuthorizationPolicyProvider may be useful include:

* Utilizzo di un servizio esterno per fornire la valutazione dei criteri.
* Utilizzando un'ampia gamma di criteri (ad esempio, per diversi numeri di stanza o età), `AuthorizationOptions.AddPolicy` quindi non ha senso aggiungere ogni singolo criterio di autorizzazione con una chiamata.
* Creazione di criteri in fase di esecuzione in base alle informazioni in un'origine dati esterna (ad esempio un database) o determinazione dinamica dei requisiti di autorizzazione tramite un altro meccanismo.

[Visualizzare o scaricare codice di esempio](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) dal repository [AspNetCore GitHub](https://github.com/dotnet/AspNetCore). Scaricare il file zip del repository dotnet/AspNetCore. Decomprimere il file. Passare alla cartella del progetto *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Personalizzare il recupero dei criteri

ASP.NET le app principali `IAuthorizationPolicyProvider` usano un'implementazione dell'interfaccia per recuperare i criteri di autorizzazione. Per impostazione predefinita, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) viene registrato e utilizzato. `DefaultAuthorizationPolicyProvider`restituisce le `AuthorizationOptions` politiche `IServiceCollection.AddAuthorization` dal fornito in una chiamata.

Personalizza questo comportamento registrando un'implementazione diversa `IAuthorizationPolicyProvider` nel contenitore di inserimento delle [dipendenze](xref:fundamentals/dependency-injection) dell'app. 

L'interfaccia `IAuthorizationPolicyProvider` contiene tre API:The interface contains three APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) restituisce un criterio di autorizzazione per un nome specificato.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) restituisce i criteri di `[Authorize]` autorizzazione predefiniti (i criteri utilizzati per gli attributi senza un criterio specificato). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) restituisce i criteri di autorizzazione di fallback (i criteri utilizzati dal middleware di autorizzazione quando non viene specificato alcun criterio). 

Implementando queste API, è possibile personalizzare la modalità di presentazione dei criteri di autorizzazione.

## <a name="parameterized-authorize-attribute-example"></a>Esempio di attributo di autorizzazione con parametriParameterized authorize attribute example

Uno scenario `IAuthorizationPolicyProvider` in cui `[Authorize]` è utile è l'abilitazione di attributi personalizzati i cui requisiti dipendono da un parametro. Ad esempio, nella documentazione relativa [all'autorizzazione basata su criteri,](xref:security/authorization/policies) un criterio basato sull'età ("AtLeast21") è stato utilizzato come esempio. Se diverse azioni del controller in un'app devono essere rese disponibili per utenti di età *diverse,* potrebbe essere utile disporre di molti criteri basati sull'età diversi. Anziché registrare tutti i diversi criteri basati `AuthorizationOptions`sull'età necessari all'applicazione `IAuthorizationPolicyProvider`in , è possibile generare i criteri in modo dinamico con un oggetto personalizzato . Per semplificare l'utilizzo dei criteri, è possibile `[MinimumAgeAuthorize(20)]`annotare le azioni con l'attributo di autorizzazione personalizzato come .

## <a name="custom-authorization-attributes"></a>Attributi di autorizzazione personalizzati

I criteri di autorizzazione sono identificati dai relativi nomi. La `MinimumAgeAuthorizeAttribute` personalizzazione descritta in precedenza deve eseguire il mapping degli argomenti in una stringa che può essere utilizzata per recuperare i criteri di autorizzazione corrispondenti. È possibile eseguire questa `AuthorizeAttribute` operazione derivando da e facendo in modo che la proprietà esetrai il `Age` wrapping della `AuthorizeAttribute.Policy` proprietà.

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

Questo tipo di `Policy` attributo ha una stringa`"MinimumAge"`basata sul prefisso hardcoded ( ) e un numero intero passato tramite il costruttore.

È possibile applicarlo alle azioni nello `Authorize` stesso modo di altri attributi, ad eccezione del fatto che accetta un numero intero come parametro.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>IAuthorizationPolicyProvider personalizzato

Il `MinimumAgeAuthorizeAttribute` costume di misura semplifica la richiesta di criteri di autorizzazione per qualsiasi età minima desiderata. Il problema successivo da risolvere è assicurarsi che i criteri di autorizzazione siano disponibili per tutte queste diverse età. Questo è `IAuthorizationPolicyProvider` dove un è utile.

Quando `MinimumAgeAuthorizationAttribute`si utilizza , i nomi `"MinimumAge" + Age`dei criteri `IAuthorizationPolicyProvider` di autorizzazione seguiranno il modello , pertanto l'impostazione personalizzata deve generare criteri di autorizzazione:

* Analisi dell'età dal nome del criterio.
* Utilizzo `AuthorizationPolicyBuilder` per creare un nuovo`AuthorizationPolicy`
* In questo e in questi esempi si presume che l'utente viene autenticato tramite un cookie. L'oggetto `AuthorizationPolicyBuilder` deve essere costruito con almeno un nome di schema di autorizzazione o avere sempre esito positivo. In caso contrario, non sono disponibili informazioni su come fornire una richiesta di verifica all'utente e verrà generata un'eccezione.
* Aggiunta di requisiti alla politica `AuthorizationPolicyBuilder.AddRequirements`in base all'età con . In altri scenari, `RequireClaim`è `RequireRole`possibile `RequireUserName` utilizzare , , o invece.

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

## <a name="multiple-authorization-policy-providers"></a>Più provider di criteri di autorizzazioneMultiple authorization policy providers

Quando si `IAuthorizationPolicyProvider` utilizzano implementazioni personalizzate, tenere presente `IAuthorizationPolicyProvider`che ASP.NET Core utilizza solo un'istanza di . Se un provider personalizzato non è in grado di fornire criteri di autorizzazione per tutti i nomi di criteri che verranno utilizzati, deve rinviare a un provider di backup. 

Si consideri, ad esempio, un'applicazione che richiede sia criteri di età personalizzati che un recupero dei criteri basato sui ruoli più tradizionale. Un'app di questo tipo potrebbe usare un provider di criteri di autorizzazione personalizzato che:Such an app could use a custom authorization policy provider that:

* Tenta di analizzare i nomi dei criteri. 
* Chiama un provider di `DefaultAuthorizationPolicyProvider`criteri diverso (ad esempio ) se il nome del criterio non contiene un'età.

L'implementazione di esempio `IAuthorizationPolicyProvider` illustrata `DefaultAuthorizationPolicyProvider` in precedenza può essere aggiornata per usare il creando un provider di criteri di backup nel costruttore (da utilizzare nel caso in cui il nome del criterio non corrisponda al modello previsto di 'MinimumAge' e l'età).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Quindi, `GetPolicyAsync` il metodo può essere `BackupPolicyProvider` aggiornato per utilizzare il invece di restituire null:Then, the method can be updated to use the instead of returning null:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Criteri predefiniti

Oltre a fornire criteri di `IAuthorizationPolicyProvider` autorizzazione denominati, è necessario implementare `GetDefaultPolicyAsync` un'autorizzazione per fornire criteri di autorizzazione per `[Authorize]` gli attributi senza un nome di criterio specificato.

In molti casi, questo attributo di autorizzazione richiede solo un utente autenticato, pertanto è possibile effettuare i criteri necessari con una chiamata a `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Come per tutti gli `IAuthorizationPolicyProvider`aspetti di un oggetto custom, è possibile personalizzarlo in base alle esigenze. In alcuni casi, può essere opportuno recuperare `IAuthorizationPolicyProvider`il criterio predefinito da un fallback.

## <a name="fallback-policy"></a>Criteri di fallback

Un'attività `IAuthorizationPolicyProvider` personalizzata `GetFallbackPolicyAsync` può facoltativamente implementare per fornire un criterio che viene utilizzato quando [si combinano i criteri](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) e quando non viene specificato alcun criterio. Se `GetFallbackPolicyAsync` restituisce un criterio non null, il criterio restituito viene utilizzato dal middleware di autorizzazione quando non vengono specificati criteri per la richiesta.

Se non sono necessari criteri di `null` fallback, il provider può restituire o rinviare al provider di fallback:If no fallback policy is required, the provider can return or defer to the fallback provider:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Usare un oggetto personalizzato IAuthorizationPolicyProviderUse a custom IAuthorizationPolicyProvider

Per utilizzare criteri `IAuthorizationPolicyProvider`personalizzati da un oggetto , è necessario:

* Registrare `AuthorizationHandler` i tipi appropriati con inserimento delle dipendenze (descritto nell'autorizzazione [basata su criteri](xref:security/authorization/policies#authorization-handlers)), come con tutti gli scenari di autorizzazione basata su criteri.
* Registrare `IAuthorizationPolicyProvider` il tipo personalizzato nella raccolta di servizi `Startup.ConfigureServices`di inserimento delle dipendenze dell'app (in ) per sostituire il provider di criteri predefinito.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Un esempio `IAuthorizationPolicyProvider` personalizzato completo è disponibile nel [repository dotnet/aspnetcore GitHub](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).
