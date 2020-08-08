---
title: ASP.NET Core Blazor WebAssembly con gruppi e ruoli di Azure Active Directory
author: guardrex
description: Informazioni su come configurare Blazor WebAssembly per l'uso di gruppi e ruoli di Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
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
ms.openlocfilehash: 6fe96616abf423df600bfdd7198e63198358e7a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013788"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="8d90b-103">Gruppi di Azure AD, ruoli amministrativi e ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="8d90b-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="8d90b-104">Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="8d90b-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="8d90b-105">Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="8d90b-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="8d90b-106">Gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="8d90b-106">User-defined groups</span></span>
  * <span data-ttu-id="8d90b-107">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="8d90b-107">Security</span></span>
  * <span data-ttu-id="8d90b-108">O365</span><span class="sxs-lookup"><span data-stu-id="8d90b-108">O365</span></span>
  * <span data-ttu-id="8d90b-109">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="8d90b-109">Distribution</span></span>
* <span data-ttu-id="8d90b-110">Ruoli</span><span class="sxs-lookup"><span data-stu-id="8d90b-110">Roles</span></span>
  * <span data-ttu-id="8d90b-111">Ruoli amministrativi predefiniti</span><span class="sxs-lookup"><span data-stu-id="8d90b-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="8d90b-112">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="8d90b-112">User-defined roles</span></span>

<span data-ttu-id="8d90b-113">Le indicazioni fornite in questo articolo sono valide per gli Blazor WebAssembly scenari di distribuzione di AAD descritti negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="8d90b-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="8d90b-114">App autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="8d90b-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="8d90b-115">App autonoma con AAD</span><span class="sxs-lookup"><span data-stu-id="8d90b-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="8d90b-116">App ospitata con AAD</span><span class="sxs-lookup"><span data-stu-id="8d90b-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="8d90b-117">Autorizzazione Microsoft Graph API</span><span class="sxs-lookup"><span data-stu-id="8d90b-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="8d90b-118">È necessaria una chiamata [API Microsoft Graph](/graph/use-the-api) per qualsiasi utente dell'app con più di cinque appartenenze ai gruppi di sicurezza e al ruolo di amministratore di AAD predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8d90b-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="8d90b-119">Per consentire le chiamate di API Graph, assegnare all'app autonoma o client di una soluzione ospitata una Blazor delle seguenti [autorizzazioni di API Graph](/graph/permissions-reference) nel portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="8d90b-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="8d90b-120">`Directory.Read.All`è l'autorizzazione con privilegi minimi e è l'autorizzazione utilizzata per l'esempio descritto in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="8d90b-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="8d90b-121">Gruppi definiti dall'utente e ruoli amministrativi predefiniti</span><span class="sxs-lookup"><span data-stu-id="8d90b-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="8d90b-122">Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure.</span><span class="sxs-lookup"><span data-stu-id="8d90b-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="8d90b-123">Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli amministrativi predefiniti.</span><span class="sxs-lookup"><span data-stu-id="8d90b-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="8d90b-124">Definizione dei ruoli con i gruppi di sicurezza di Azure AD</span><span class="sxs-lookup"><span data-stu-id="8d90b-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="8d90b-125">`groupMembershipClaims`attributo</span><span class="sxs-lookup"><span data-stu-id="8d90b-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="8d90b-126">Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* incorporato di AAD.</span><span class="sxs-lookup"><span data-stu-id="8d90b-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="8d90b-127">La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="8d90b-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="8d90b-128">L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="8d90b-129">Quando il numero di ruoli amministrativi di Azure e gruppi definiti dall'utente assegnati è superiore a cinque, AAD Invia un' `hasgroups` attestazione con un `true` valore anziché inviare un' `groups` attestazione.</span><span class="sxs-lookup"><span data-stu-id="8d90b-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="8d90b-130">Qualsiasi app che può avere più di cinque ruoli e gruppi assegnati ai propri utenti deve effettuare una chiamata separata API Graph per ottenere i ruoli e i gruppi di un utente.</span><span class="sxs-lookup"><span data-stu-id="8d90b-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="8d90b-131">L'implementazione di esempio disponibile in questo articolo illustra questo scenario.</span><span class="sxs-lookup"><span data-stu-id="8d90b-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="8d90b-132">Per altre informazioni, vedere l' `groups` articolo relativo alle `hasgroups` informazioni sulle attestazioni e in [token di accesso della piattaforma Microsoft Identity: payload Claims](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="8d90b-133">Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.</span><span class="sxs-lookup"><span data-stu-id="8d90b-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="8d90b-134">Assegnare una matrice vuota a ogni proprietà in modo che il controllo `null` non sia necessario quando queste proprietà vengono usate nei cicli in un `foreach` secondo momento.</span><span class="sxs-lookup"><span data-stu-id="8d90b-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="8d90b-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d90b-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="8d90b-136">Nell'app autonoma o nell'app client di una soluzione ospitata Blazor creare una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="8d90b-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="8d90b-137">Utilizzare l'ambito corretto (autorizzazione) per le chiamate API Graph che ottengono informazioni sui ruoli e sui gruppi.</span><span class="sxs-lookup"><span data-stu-id="8d90b-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="8d90b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d90b-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="8d90b-139">In `Program.Main` ( `Program.cs` ) aggiungere il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio di implementazione e aggiungere un oggetto denominato <xref:System.Net.Http.HttpClient> per eseguire richieste di API Graph.</span><span class="sxs-lookup"><span data-stu-id="8d90b-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="8d90b-140">Nell'esempio seguente viene denominato il client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="8d90b-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="8d90b-141">Creare classi di oggetti directory di AAD per ricevere i ruoli e i gruppi di Open Data Protocol (OData) da una chiamata di API Graph.</span><span class="sxs-lookup"><span data-stu-id="8d90b-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="8d90b-142">OData arriva in formato JSON e una chiamata a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popola un'istanza della `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="8d90b-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="8d90b-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d90b-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="8d90b-144">Creare una factory utente personalizzata per elaborare le attestazioni dei ruoli e dei gruppi.</span><span class="sxs-lookup"><span data-stu-id="8d90b-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="8d90b-145">Nell'implementazione di esempio seguente viene anche gestita la `roles` matrice di attestazioni, che viene analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="8d90b-146">Se l' `hasgroups` attestazione è presente, il nome <xref:System.Net.Http.HttpClient> viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente.</span><span class="sxs-lookup"><span data-stu-id="8d90b-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="8d90b-147">Questa implementazione usa l'endpoint di Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentazione dell'API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="8d90b-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="8d90b-148">Le linee guida in questo argomento verranno aggiornate per Identity la versione 2.0 quando i pacchetti MSAL vengono aggiornati per la versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="8d90b-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="8d90b-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="8d90b-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="8d90b-150">Non è necessario fornire codice per rimuovere l' `groups` attestazione originale, se presente, perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8d90b-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="8d90b-151">L'approccio in questo esempio:</span><span class="sxs-lookup"><span data-stu-id="8d90b-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="8d90b-152">Aggiunge una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata per alleghire i token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="8d90b-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="8d90b-153">Aggiunge un oggetto denominato <xref:System.Net.Http.HttpClient> per l'esecuzione di richieste API Web a un endpoint sicuro dell'API Web esterno.</span><span class="sxs-lookup"><span data-stu-id="8d90b-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="8d90b-154">Usa il denominato <xref:System.Net.Http.HttpClient> per eseguire richieste autorizzate.</span><span class="sxs-lookup"><span data-stu-id="8d90b-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="8d90b-155">Il code coverage generale per questo approccio è disponibile in questo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> articolo.</span><span class="sxs-lookup"><span data-stu-id="8d90b-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="8d90b-156">Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o dell'app client di una Blazor soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="8d90b-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="8d90b-157">Consenso all' `Directory.Read.All` ambito di autorizzazione come ambito aggiuntivo per l'app:</span><span class="sxs-lookup"><span data-stu-id="8d90b-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

```csharp
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

<span data-ttu-id="8d90b-158">Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="8d90b-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="8d90b-159">L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* incorporato di AAD:</span><span class="sxs-lookup"><span data-stu-id="8d90b-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="8d90b-160">Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID del gruppo di ruoli di AAD Adminstrative](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="8d90b-161">Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.</span><span class="sxs-lookup"><span data-stu-id="8d90b-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="8d90b-162">Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:</span><span class="sxs-lookup"><span data-stu-id="8d90b-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

<span data-ttu-id="8d90b-163">L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="8d90b-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="8d90b-164">Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.</span><span class="sxs-lookup"><span data-stu-id="8d90b-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="8d90b-165">Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="8d90b-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="8d90b-166">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="8d90b-166">User-defined roles</span></span>

<span data-ttu-id="8d90b-167">È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="8d90b-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="8d90b-168">Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="8d90b-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="8d90b-169">Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:</span><span class="sxs-lookup"><span data-stu-id="8d90b-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="8d90b-170">Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard.</span><span class="sxs-lookup"><span data-stu-id="8d90b-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="8d90b-171">Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="8d90b-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="8d90b-172">Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="8d90b-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="8d90b-173">La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="8d90b-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="8d90b-174">L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="8d90b-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="8d90b-175">Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="8d90b-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="8d90b-176">Aggiungere e registrare `CustomUserFactory` nell'app autonoma o client di una soluzione ospitata Blazor , come illustrato nella sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="8d90b-177">Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="8d90b-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="8d90b-178">Nell'app `Program.Main` autonoma o nell'app client di una soluzione ospitata Blazor specificare l'attestazione denominata " `role` " come attestazione del ruolo:</span><span class="sxs-lookup"><span data-stu-id="8d90b-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="8d90b-179">A questo punto sono funzionali gli approcci di autorizzazione per i componenti.</span><span class="sxs-lookup"><span data-stu-id="8d90b-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="8d90b-180">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="8d90b-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="8d90b-181">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="8d90b-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="8d90b-182">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="8d90b-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="8d90b-183">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="8d90b-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="8d90b-184">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="8d90b-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="8d90b-185">ID gruppo di ruoli di AAD Adminstrative</span><span class="sxs-lookup"><span data-stu-id="8d90b-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="8d90b-186">Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="8d90b-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="8d90b-187">I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app.</span><span class="sxs-lookup"><span data-stu-id="8d90b-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="8d90b-188">Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli amministrativi predefiniti di AAD](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="8d90b-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="8d90b-189">Ruolo amministrativo AAD</span><span class="sxs-lookup"><span data-stu-id="8d90b-189">AAD Administrative Role</span></span> | <span data-ttu-id="8d90b-190">ID dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="8d90b-190">Object ID</span></span>
--- | ---
<span data-ttu-id="8d90b-191">Amministratore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="8d90b-191">Application administrator</span></span> | <span data-ttu-id="8d90b-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="8d90b-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="8d90b-193">Sviluppatore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="8d90b-193">Application developer</span></span> | <span data-ttu-id="8d90b-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="8d90b-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="8d90b-195">Amministratore dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="8d90b-195">Authentication administrator</span></span> | <span data-ttu-id="8d90b-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="8d90b-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="8d90b-197">Amministratore di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="8d90b-197">Azure DevOps administrator</span></span> | <span data-ttu-id="8d90b-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="8d90b-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="8d90b-199">Amministratore di Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="8d90b-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="8d90b-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="8d90b-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="8d90b-201">Amministratore keyset Framework dell'esperienza B2C</span><span class="sxs-lookup"><span data-stu-id="8d90b-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="8d90b-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="8d90b-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="8d90b-203">Amministratore criteri B2C Framework dell'esperienza</span><span class="sxs-lookup"><span data-stu-id="8d90b-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="8d90b-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="8d90b-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="8d90b-205">Amministratore flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="8d90b-205">B2C user flow administrator</span></span> | <span data-ttu-id="8d90b-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="8d90b-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="8d90b-207">Amministratore attributi flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="8d90b-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="8d90b-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="8d90b-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="8d90b-209">Amministratore fatturazione</span><span class="sxs-lookup"><span data-stu-id="8d90b-209">Billing administrator</span></span> | <span data-ttu-id="8d90b-210">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="8d90b-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="8d90b-211">Amministratore di applicazioni cloud</span><span class="sxs-lookup"><span data-stu-id="8d90b-211">Cloud application administrator</span></span> | <span data-ttu-id="8d90b-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="8d90b-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="8d90b-213">Amministratore dispositivo cloud</span><span class="sxs-lookup"><span data-stu-id="8d90b-213">Cloud device administrator</span></span> | <span data-ttu-id="8d90b-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="8d90b-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="8d90b-215">Amministratore di conformità</span><span class="sxs-lookup"><span data-stu-id="8d90b-215">Compliance administrator</span></span> | <span data-ttu-id="8d90b-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="8d90b-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="8d90b-217">Amministratore dei dati sulla conformità</span><span class="sxs-lookup"><span data-stu-id="8d90b-217">Compliance data administrator</span></span> | <span data-ttu-id="8d90b-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="8d90b-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="8d90b-219">Amministratore di accesso condizionale</span><span class="sxs-lookup"><span data-stu-id="8d90b-219">Conditional Access administrator</span></span> | <span data-ttu-id="8d90b-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="8d90b-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="8d90b-221">Responsabile approvazione per l'accesso a Customer Lockbox</span><span class="sxs-lookup"><span data-stu-id="8d90b-221">Customer LockBox access approver</span></span> | <span data-ttu-id="8d90b-222">c18d54a8-B13E-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="8d90b-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="8d90b-223">Amministratore di analisi desktop</span><span class="sxs-lookup"><span data-stu-id="8d90b-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="8d90b-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="8d90b-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="8d90b-225">Ruoli con autorizzazioni di lettura nella directory</span><span class="sxs-lookup"><span data-stu-id="8d90b-225">Directory readers</span></span> | <span data-ttu-id="8d90b-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="8d90b-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="8d90b-227">Amministratore di Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="8d90b-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="8d90b-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="8d90b-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="8d90b-229">Amministratori di Exchange</span><span class="sxs-lookup"><span data-stu-id="8d90b-229">Exchange administrator</span></span> | <span data-ttu-id="8d90b-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="8d90b-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="8d90b-231">IdentityAmministratore provider esterno</span><span class="sxs-lookup"><span data-stu-id="8d90b-231">External Identity Provider administrator</span></span> | <span data-ttu-id="8d90b-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="8d90b-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="8d90b-233">Amministratore globale</span><span class="sxs-lookup"><span data-stu-id="8d90b-233">Global administrator</span></span> | <span data-ttu-id="8d90b-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="8d90b-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="8d90b-235">Ruolo con autorizzazioni di lettura globali</span><span class="sxs-lookup"><span data-stu-id="8d90b-235">Global reader</span></span> | <span data-ttu-id="8d90b-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="8d90b-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="8d90b-237">Amministratore di gruppi</span><span class="sxs-lookup"><span data-stu-id="8d90b-237">Groups administrator</span></span> | <span data-ttu-id="8d90b-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="8d90b-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="8d90b-239">Mittente dell'invito guest</span><span class="sxs-lookup"><span data-stu-id="8d90b-239">Guest inviter</span></span> | <span data-ttu-id="8d90b-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="8d90b-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="8d90b-241">Amministratore dell'help desk</span><span class="sxs-lookup"><span data-stu-id="8d90b-241">Helpdesk administrator</span></span> | <span data-ttu-id="8d90b-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="8d90b-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="8d90b-243">Amministratore di Intune</span><span class="sxs-lookup"><span data-stu-id="8d90b-243">Intune administrator</span></span> | <span data-ttu-id="8d90b-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="8d90b-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="8d90b-245">Amministratore di Kaizala</span><span class="sxs-lookup"><span data-stu-id="8d90b-245">Kaizala administrator</span></span> | <span data-ttu-id="8d90b-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="8d90b-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="8d90b-247">Amministratore licenze</span><span class="sxs-lookup"><span data-stu-id="8d90b-247">License administrator</span></span> | <span data-ttu-id="8d90b-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="8d90b-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="8d90b-249">Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="8d90b-249">Message center privacy reader</span></span> | <span data-ttu-id="8d90b-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="8d90b-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="8d90b-251">Ruolo con autorizzazioni di lettura per il Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="8d90b-251">Message center reader</span></span> | <span data-ttu-id="8d90b-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="8d90b-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="8d90b-253">Amministratore delle app di Office</span><span class="sxs-lookup"><span data-stu-id="8d90b-253">Office apps administrator</span></span> | <span data-ttu-id="8d90b-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="8d90b-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="8d90b-255">Amministratore password</span><span class="sxs-lookup"><span data-stu-id="8d90b-255">Password administrator</span></span> | <span data-ttu-id="8d90b-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="8d90b-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="8d90b-257">Amministratore di Power BI</span><span class="sxs-lookup"><span data-stu-id="8d90b-257">Power BI administrator</span></span> | <span data-ttu-id="8d90b-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="8d90b-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="8d90b-259">Amministratore di Power Platform</span><span class="sxs-lookup"><span data-stu-id="8d90b-259">Power platform administrator</span></span> | <span data-ttu-id="8d90b-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="8d90b-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="8d90b-261">Amministratore autenticazione con privilegi</span><span class="sxs-lookup"><span data-stu-id="8d90b-261">Privileged authentication administrator</span></span> | <span data-ttu-id="8d90b-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="8d90b-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="8d90b-263">Amministratore dei ruoli con privilegi</span><span class="sxs-lookup"><span data-stu-id="8d90b-263">Privileged role administrator</span></span> | <span data-ttu-id="8d90b-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="8d90b-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="8d90b-265">Lettore di report</span><span class="sxs-lookup"><span data-stu-id="8d90b-265">Reports reader</span></span> | <span data-ttu-id="8d90b-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="8d90b-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="8d90b-267">Amministratore della ricerca</span><span class="sxs-lookup"><span data-stu-id="8d90b-267">Search administrator</span></span> | <span data-ttu-id="8d90b-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="8d90b-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="8d90b-269">Editor della ricerca</span><span class="sxs-lookup"><span data-stu-id="8d90b-269">Search editor</span></span> | <span data-ttu-id="8d90b-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="8d90b-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="8d90b-271">Amministratore della sicurezza</span><span class="sxs-lookup"><span data-stu-id="8d90b-271">Security administrator</span></span> | <span data-ttu-id="8d90b-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="8d90b-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="8d90b-273">Operatore per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="8d90b-273">Security operator</span></span> | <span data-ttu-id="8d90b-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="8d90b-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="8d90b-275">Ruolo con autorizzazioni di lettura per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="8d90b-275">Security reader</span></span> | <span data-ttu-id="8d90b-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="8d90b-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="8d90b-277">Amministratore del supporto per il servizio</span><span class="sxs-lookup"><span data-stu-id="8d90b-277">Service support administrator</span></span> | <span data-ttu-id="8d90b-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="8d90b-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="8d90b-279">Amministratore di SharePoint</span><span class="sxs-lookup"><span data-stu-id="8d90b-279">SharePoint administrator</span></span> | <span data-ttu-id="8d90b-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="8d90b-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="8d90b-281">Amministratore di Skype for Business</span><span class="sxs-lookup"><span data-stu-id="8d90b-281">Skype for Business administrator</span></span> | <span data-ttu-id="8d90b-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="8d90b-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="8d90b-283">Amministratore comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="8d90b-283">Teams Communications Administrator</span></span> | <span data-ttu-id="8d90b-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="8d90b-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="8d90b-285">Tecnico di supporto comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="8d90b-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="8d90b-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="8d90b-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="8d90b-287">Specialista comunicazione Teams</span><span class="sxs-lookup"><span data-stu-id="8d90b-287">Teams Communications Specialist</span></span> | <span data-ttu-id="8d90b-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="8d90b-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="8d90b-289">Amministratore del servizio Teams</span><span class="sxs-lookup"><span data-stu-id="8d90b-289">Teams Service Administrator</span></span> | <span data-ttu-id="8d90b-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="8d90b-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="8d90b-291">Amministratore utenti</span><span class="sxs-lookup"><span data-stu-id="8d90b-291">User administrator</span></span> | <span data-ttu-id="8d90b-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="8d90b-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d90b-293">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8d90b-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
