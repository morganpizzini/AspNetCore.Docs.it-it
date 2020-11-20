---
title: ASP.NET Core Blazor WebAssembly con gruppi e ruoli di Azure Active Directory
author: guardrex
description: Informazioni su come configurare Blazor WebAssembly per l'uso di gruppi e ruoli di Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ded70f028b3021574ba260838837d9b23abd72f1
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981882"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>Gruppi di Azure Active Directory (AAD), ruoli di amministratore e ruoli definiti dall'utente

Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)

> [!NOTE]
> Questo articolo si applica alle Blazor app ASP.NET Core versione 3,1 con Microsoft Identity v 1.0 ed è pianificato per l'aggiornamento a 5,0 con Identity v 2.0. Per altre informazioni, vedere [ Blazor WASM con i ruoli e i gruppi AAD/B2C (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683).

Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :

* Gruppi definiti dall'utente
  * Sicurezza
  * Microsoft 365
  * Distribuzione
* Ruoli
  * Ruoli di amministratore di AAD
  * Ruoli definiti dall'utente

Le indicazioni fornite in questo articolo sono valide per gli Blazor WebAssembly scenari di distribuzione di AAD descritti negli argomenti seguenti:

* [App autonoma con account Microsoft](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [App autonoma con AAD](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [App ospitata con AAD](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a>Ambiti

È necessaria una chiamata [API Microsoft Graph](/graph/use-the-api) per qualsiasi utente dell'app con più di cinque appartenenze ai gruppi di sicurezza e ai ruoli di amministratore di AAD.

Per consentire le chiamate di API Graph, assegnare all'app autonoma o *`Client`* di una soluzione ospitata una Blazor delle seguenti [autorizzazioni di API Graph (ambiti)](/graph/permissions-reference) nel portale di Azure:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` è l'ambito con privilegi minimi e è l'ambito usato per l'esempio descritto in questo articolo.

## <a name="user-defined-groups-and-administrator-roles"></a>Gruppi definiti dall'utente e ruoli di amministratore

Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure. Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli di amministratore di AAD.

* [Definizione dei ruoli con i gruppi di sicurezza di Azure AD](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims` attributo](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* di AAD.

La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON. L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .

Quando il numero di ruoli di amministratore e gruppi definiti dall'utente assegnati è superiore a cinque, AAD Invia un' `hasgroups` attestazione con un `true` valore anziché inviare un' `groups` attestazione. Qualsiasi app che può avere più di cinque ruoli e gruppi assegnati ai propri utenti deve effettuare una chiamata separata API Graph per ottenere i ruoli e i gruppi di un utente. L'implementazione di esempio disponibile in questo articolo illustra questo scenario. Per altre informazioni, vedere l' `groups` articolo relativo alle `hasgroups` informazioni sulle attestazioni e in [token di accesso della piattaforma Microsoft Identity: payload Claims](/azure/active-directory/develop/access-tokens#payload-claims) .

Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli. Assegnare una matrice vuota a ogni proprietà in modo che il controllo `null` non sia necessario quando queste proprietà vengono usate nei cicli in un `foreach` secondo momento.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

::: moniker range=">= aspnetcore-5.0"

Usare **uno** degli approcci seguenti per creare attestazioni per i gruppi e i ruoli di AAD:

* [Usare Graph SDK](#use-the-graph-sdk)
* [Usa un oggetto denominato `HttpClient`](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a>Usare Graph SDK

Aggiungere un riferimento al pacchetto all'app autonoma o all' *`Client`* app di una soluzione ospitata Blazor per [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .

Aggiungere le classi e la configurazione dell'utilità Graph SDK nella sezione *Graph SDK* dell' <xref:blazor/security/webassembly/graph-api#graph-sdk> articolo.

Aggiungere la factory di account utente personalizzata seguente alla APPO autonoma o all' *`Client`* app di una soluzione ospitata Blazor ( `CustomAccountFactory.cs` ). La Factory utente personalizzata viene usata per elaborare le attestazioni di ruoli e gruppi. La `roles` matrice di attestazioni è analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) . Se l' `hasgroups` attestazione è presente, Graph SDK viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Il codice precedente non include le appartenenze transitive. Se l'app richiede attestazioni di appartenenza a gruppi dirette e transitive:

* Modificare il `IUserMemberOfCollectionWithReferencesPage` tipo per `groupsAndAzureRoles` in `IUserTransitiveMemberOfCollectionWithReferencesPage` .
* Quando si richiedono i gruppi e i ruoli dell'utente, sostituire la `MemberOf` proprietà con `TransitiveMemberOf` .

In `Program.Main` ( `Program.cs` ) configurare l'autenticazione MSAL per l'uso della factory dell'account utente personalizzata: se l'app usa una classe di account utente personalizzata che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , scambiare la classe di account utente personalizzata per <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> nel codice seguente:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

### <a name="use-a-named-httpclient"></a>Usa un oggetto denominato `HttpClient`

::: moniker-end

Nell'app autonoma o nell' *`Client`* app di una soluzione ospitata Blazor creare una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata. Utilizzare l'ambito corretto per API Graph le chiamate che ottengono informazioni sui ruoli e sui gruppi.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

In `Program.Main` ( `Program.cs` ) aggiungere il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio di implementazione e aggiungere un oggetto denominato <xref:System.Net.Http.HttpClient> per eseguire richieste di API Graph. Nell'esempio seguente viene denominato il client `GraphAPI` :

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Creare classi di oggetti directory di AAD per ricevere i ruoli e i gruppi di Open Data Protocol (OData) da una chiamata di API Graph. OData arriva in formato JSON e una chiamata a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popola un'istanza della `DirectoryObjects` classe.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Creare una factory utente personalizzata per elaborare le attestazioni dei ruoli e dei gruppi. Nell'implementazione di esempio seguente viene anche gestita la `roles` matrice di attestazioni, che viene analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) . Se l' `hasgroups` attestazione è presente, il nome <xref:System.Net.Http.HttpClient> viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente. Questa implementazione usa l'endpoint di Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentazione dell'API](/graph/api/user-list-memberof)).

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {Message}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Non è necessario fornire codice per rimuovere l' `groups` attestazione originale, se presente, perché viene automaticamente rimossa dal Framework.

> [!NOTE]
> L'approccio in questo esempio:
>
> * Aggiunge una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata per alleghire i token di accesso alle richieste in uscita.
> * Aggiunge un oggetto denominato <xref:System.Net.Http.HttpClient> per l'esecuzione di richieste API Web a un endpoint sicuro dell'API Web esterno.
> * Usa il denominato <xref:System.Net.Http.HttpClient> per eseguire richieste autorizzate.
>
> Il code coverage generale per questo approccio è disponibile in questo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> articolo.

Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o *`Client`* dell'app di una Blazor soluzione ospitata. Consenso all' `Directory.Read.All` ambito come ambito aggiuntivo per l'app:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

## <a name="authorization-configuration"></a>Configurazione dell'autorizzazione

Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` . L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* di AAD:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids) .

Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.

Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.

Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autorizzare l'accesso all'API server per i ruoli di amministratore e i gruppi definiti dall'utente

Oltre ad autorizzare gli utenti nell'app webassembly sul lato client ad accedere a pagine e risorse, l'API server può autorizzare gli utenti ad accedere agli endpoint API protetti. Dopo che l'app *Server* convalida il token di accesso dell'utente:

* L'app per le API server usa l' [attestazione dell'identificatore di oggetto `oid` ](/azure/active-directory/develop/id-tokens#payload-claims) non modificabile dell'utente () dal relativo token di accesso per ottenere un token di accesso per API Graph.
* Una chiamata API Graph ottiene le appartenenze ai ruoli di amministratore e del gruppo di sicurezza definiti dall'utente di Azure dell'utente chiamando [`memberOf`](/graph/api/user-list-memberof) sull'utente.
* Le appartenenze vengono usate per stabilire le `group` attestazioni.
* È possibile usare i [criteri di autorizzazione](xref:security/authorization/policies) per limitare l'accesso degli utenti agli endpoint dell'API server nell'intera app.

> [!NOTE]
> Questa guida attualmente non include l'autorizzazione degli utenti sulla base dei [ruoli definiti dall'utente di AAD](#user-defined-roles).

Le indicazioni fornite in questa sezione configurano l'app per le API server come [*app daemon*](/azure/active-directory/develop/scenario-daemon-overview) per la chiamata API Microsoft Graph. Questo approccio **non**:

* Richiedere l' `access_as_user` ambito.
* Accedere API Graph per conto dell'utente o del client che effettua la richiesta dell'API.

La chiamata a API Graph dall'app per le API server richiede solo un' **applicazione** per l'app per le api Server API Graph ambito per `Directory.Read.All` nel portale di Azure. Questo approccio impedisce assolutamente all'app client di accedere ai dati della directory non consentiti in modo esplicito dall'API del server. L'app client può accedere solo agli endpoint controller dell'app per le API del server.

### <a name="azure-configuration"></a>Configurazione di Azure

* Verificare che alla registrazione dell'app *Server* venga assegnata l' **applicazione** (non **delegata**) API Graph ambito per `Directory.Read.All` , che rappresenta il livello di accesso con privilegi minimi per i gruppi di sicurezza. Verificare che l'autorizzazione dell'amministratore venga applicata all'ambito dopo aver eseguito l'assegnazione dell'ambito.
* Assegnare un nuovo segreto client all'app *Server* . Prendere nota del segreto per la configurazione dell'app nella sezione [impostazioni app](#app-settings) .

### <a name="app-settings"></a>Impostazioni app

Nel file di impostazioni dell'app ( `appsettings.json` o `appsettings.Production.json` ) creare una `ClientSecret` voce con il segreto client dell'app *Server* dalla portale di Azure:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Ad esempio:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Se il dominio dell'editore tenant non viene verificato, l'ambito dell'API server per l'accesso utente/client usa un `https://` URI basato su. In questo scenario l'app per le API server richiede la `Audience` configurazione nel `appsettings.json` file. Nella configurazione seguente, la fine del valore non `Audience` include l' **not** ambito predefinito `/{DEFAULT SCOPE}` , in cui il segnaposto `{DEFAULT SCOPE}` è l'ambito predefinito:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> Nella configurazione dell'esempio precedente:
>
> * Il dominio del tenant è `contoso.onmicrosoft.com` .
> * L'app per le API del server `ClientId` è `41451fa7-82d9-4673-8fa5-69eff5a761fd` .
>
> > [!NOTE]
> > `Audience` **Non** è in genere necessario configurare un oggetto in modo esplicito per le app con un dominio Publisher verificato con `api://` ambito API basato su.
>
> Per altre informazioni, vedere <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.

::: moniker-end

### <a name="authorization-policies"></a>I Criteri di autorizzazione

Creare [criteri di autorizzazione](xref:security/authorization/policies) per i gruppi di sicurezza AAD e i ruoli di amministratore di AAD nell'app *Server* `Startup.ConfigureServices` ( `Startup.cs` ) basata sugli ID oggetto gruppo e gli [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids).

Un criterio del ruolo amministratore fatturazione di Azure, ad esempio, ha la configurazione seguente:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Per altre informazioni, vedere <xref:security/authorization/policies>.

### <a name="controller-access"></a>Accesso al controller

Richiedere criteri nei controller dell'app *Server* .

Nell'esempio seguente viene limitato l'accesso ai dati di fatturazione da `BillingDataController` ad amministratori della fatturazione di Azure con un nome di criterio `BillingAdmin` , come configurato nella sezione [criteri di autorizzazione](#authorization-policies) :

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a>Pacchetti

Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity . Client](https://www.nuget.org/packages/Microsoft.Identity.Client)

### <a name="services"></a>Servizi

Nel metodo *Server* dell'app Server `Startup.ConfigureServices` sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* . Aggiungere gli spazi dei nomi seguenti a `Startup.cs` :

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Ad esempio, l'app può registrare l'autenticazione non riuscita.
* In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi. Attiva solo le informazioni personali per il debug con gli account utente di test.

In `Startup.ConfigureServices`:

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

Nel codice precedente, la gestione degli errori di token seguenti è facoltativa:

* `MsalUiRequiredException`: L'app non ha autorizzazioni sufficienti (ambiti).
  * Determinare se gli ambiti delle app per le API del server nel portale di Azure includono un'autorizzazione per l' **applicazione** `Directory.Read.All` .
  * Verificare che l'amministratore tenant abbia concesso le autorizzazioni per l'app.
* `MsalServiceException` ( `AADSTS70011` ): Verificare che l'ambito sia `https://graph.microsoft.com/.default` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a>Pacchetti

Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="service-configuration"></a>Configurazione del servizio

Nel metodo *Server* dell'app Server `Startup.ConfigureServices` aggiungere la logica per eseguire la chiamata API Graph e stabilire le `group` attestazioni utente per i gruppi di sicurezza e i ruoli dell'utente.

> [!NOTE]
> Il codice di esempio in questa sezione usa il Active Directory Authentication Library (ADAL), che è basato su Microsoft Identity Platform v 1.0.

Sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* . Il set di `using` istruzioni seguente include gli spazi dei nomi richiesti per il codice seguente in questa sezione:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :

* Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> . Ad esempio, l'app può registrare l'autenticazione non riuscita.
* In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi. Attiva solo le informazioni personali per il debug con gli account utente di test.

In `Startup.ConfigureServices`:

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

Nell'esempio precedente:

* <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Viene effettuato un tentativo di acquisizione del token invisibile all'utente () perché il token di accesso potrebbe essere già stato archiviato nella cache dei token adal. È più veloce ottenere il token dalla cache anziché richiedere un nuovo token.
* Se il token di accesso non viene acquisito dalla cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> viene generata), viene eseguita un'asserzione utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) con la credenziale client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) per ottenere il token per conto dell'utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ). Successivamente, il `Microsoft.Graph.GraphServiceClient` può continuare a usare il token per effettuare la chiamata API Graph. Il token viene inserito nella cache dei token ADAL. Per API Graph future chiamate per lo stesso utente, il token viene acquisito dalla cache in modo invisibile all'utente con <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .

::: moniker-end

Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non ottiene le appartenenze transitive. Per modificare il codice per ottenere l'appartenenza diretta e transitiva:

* Per la riga di codice:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Sostituire la riga precedente con:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Per la riga di codice:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Sostituire la riga precedente con:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non distingue tra i gruppi di sicurezza AAD e i ruoli di amministratore di AAD durante la creazione di attestazioni. Per fare in modo che l'app distingua tra gruppi e ruoli, controllare `entry.ODataType` quando si scorrono i gruppi e i ruoli. Per creare attestazioni di ruolo e gruppi di sicurezza separati, usare codice simile al seguente:

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a>Ruoli definiti dall'utente

È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.

Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.

Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:

* `admin`
* `developer`

> [!NOTE]
> Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard. Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.
>
> Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.

La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON. L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.

Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON. Aggiungere e registrare `CustomUserFactory` nell'app autonoma o in *`Client`* un'app di una Blazor soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) . Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.

Nell'app `Program.Main` autonoma o *`Client`* app di una soluzione ospitata Blazor specificare l'attestazione denominata " `role` " come attestazione del ruolo:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

A questo punto sono funzionali gli approcci di autorizzazione per i componenti. Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:

* [ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )
* [Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )

  Sono supportati più test di ruolo:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a>ID oggetto ruolo amministratore AAD

Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni. I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app. Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .

Ruolo amministratore AAD | ID dell'oggetto.
--- | ---
Amministratore di applicazioni | fa11557b-4f15-4ddd-85d5-313c7cd74047
Sviluppatore di applicazioni | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Amministratore dell'autenticazione | 02d110a1-96b1-419e-af87-746461b60ed7
Amministratore di Azure DevOps | a5311ace-ca41-44cd-b833-8d22caa0b34f
Amministratore di Azure Information Protection | 18632dce-f9b5-4f01-abb5-37051f06860e
Amministratore keyset Framework dell'esperienza B2C | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
Amministratore criteri B2C Framework dell'esperienza | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
Amministratore flusso utente B2C | baa531b7-8cf0-44ad-8f98-eded88dae827
Amministratore attributi flusso utente B2C | dd0baca0-a535-48c1-b871-8431abe16452
Amministratore fatturazione | 69ff516a-B57D-4697-A429-9de4af7b5609
Amministratore di applicazioni cloud | 250b5fe3-b553-458d-9a53-b782c13c34bf
Amministratore dispositivo cloud | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Amministratore di conformità | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Amministratore dei dati sulla conformità | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Amministratore di accesso condizionale | 8f71a611-137d-49af-87ad-e97f1fd5da76
Responsabile approvazione per l'accesso a Customer Lockbox | c18d54a8-B13E-4954-A1A4-7deaf2e4f184
Amministratore di analisi desktop | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Ruoli con autorizzazioni di lettura nella directory | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Amministratore di Dynamics 365 | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Amministratori di Exchange | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
IdentityAmministratore provider esterno | febfaeb4-e478-407a-b4b3-f4d9716618a2
Amministratore globale | a45ba61b-44db-462c-924b-3b2719152588
Ruolo con autorizzazioni di lettura globali | f6903b21-6aba-4124-b44c-76671796b9d5
Amministratore di gruppi | 158b3e5a-d89d-460b-92b5-3b34985f0197
Mittente dell'invito guest | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Amministratore dell'help desk | 108678c8-6628-44e1-8d01-caf598a6a5f5
Amministratore di Intune | 79950741-23fa-4189-b2cb-46640601c497
Amministratore di Kaizala | d6322af2-48e7-42e0-8c68-0bbe31af3412
Amministratore licenze | 3355458a-e423-44bf-8b98-4ac5e572cea5
Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Ruolo con autorizzazioni di lettura per il Centro messaggi | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Amministratore delle app di Office | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Amministratore password | 466e48b7-5d66-4ae5-8911-1a118de74941
Amministratore di Power BI | 984e83b8-8337-4255-91a1-acb663175ab4
Amministratore di Power Platform | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Amministratore autenticazione con privilegi | 0829f731-b46d-419f-9742-aeb122367d11
Amministratore dei ruoli con privilegi | f20a725a-d1c8-4107-83ea-1171c97d00c7
Lettore di report | 54635450-e8ed-4f2d-9632-07db2517b4de
Amministratore della ricerca | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Editor della ricerca | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Amministratore della sicurezza | 20fa50e3-6531-44d8-bd39-b251420568ad
Operatore per la sicurezza | 43aae017-8e51-4188-91ab-e6debd572800
Ruolo con autorizzazioni di lettura per la sicurezza | 45035cd3-fd97-4250-8197-3a53d3562d9b
Amministratore del supporto per il servizio | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
Amministratore di SharePoint | e1c32229-875e-461d-ae24-3cb99116e86c
Amministratore di Skype for Business | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Amministratore comunicazioni Teams | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Tecnico di supporto comunicazioni Teams | 802dd94e-d717-46f6-af98-b9167071e9fc
Specialista comunicazione Teams | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Amministratore del servizio Teams | 8846a0be-197b-443a-b13c-11192691fa24
Amministratore utenti | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
