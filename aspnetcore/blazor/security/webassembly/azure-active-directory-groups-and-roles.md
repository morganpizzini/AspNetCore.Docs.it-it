---
title: ASP.NET Core Blazor WebAssembly con gruppi e ruoli di Azure Active Directory
author: guardrex
description: Informazioni su come configurare Blazor WebAssembly per l'uso di gruppi e ruoli di Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: ac666a4c7493140d4ae93047e18202c3d8314c7b
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900700"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="7e0d8-103">Gruppi di Azure Active Directory (AAD), ruoli di amministratore e ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="7e0d8-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="7e0d8-104">Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="7e0d8-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="7e0d8-105">Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="7e0d8-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="7e0d8-106">Gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="7e0d8-106">User-defined groups</span></span>
  * <span data-ttu-id="7e0d8-107">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="7e0d8-107">Security</span></span>
  * <span data-ttu-id="7e0d8-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="7e0d8-108">Microsoft 365</span></span>
  * <span data-ttu-id="7e0d8-109">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="7e0d8-109">Distribution</span></span>
* <span data-ttu-id="7e0d8-110">Ruoli</span><span class="sxs-lookup"><span data-stu-id="7e0d8-110">Roles</span></span>
  * <span data-ttu-id="7e0d8-111">Ruoli di amministratore di AAD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="7e0d8-112">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="7e0d8-112">User-defined roles</span></span>

<span data-ttu-id="7e0d8-113">Le indicazioni fornite in questo articolo sono valide per gli Blazor WebAssembly scenari di distribuzione di AAD descritti negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="7e0d8-114">App autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="7e0d8-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="7e0d8-115">App autonoma con AAD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="7e0d8-116">App ospitata con AAD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="7e0d8-117">Autorizzazione Microsoft Graph API</span><span class="sxs-lookup"><span data-stu-id="7e0d8-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="7e0d8-118">È necessaria una chiamata [API Microsoft Graph](/graph/use-the-api) per qualsiasi utente dell'app con più di cinque appartenenze ai gruppi di sicurezza e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="7e0d8-119">Per consentire le chiamate di API Graph, assegnare all'app autonoma o *`Client`* di una soluzione ospitata una Blazor delle seguenti [autorizzazioni di API Graph](/graph/permissions-reference) nel portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="7e0d8-120">`Directory.Read.All` è l'autorizzazione con privilegi minimi e è l'autorizzazione utilizzata per l'esempio descritto in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="7e0d8-121">Gruppi definiti dall'utente e ruoli di amministratore</span><span class="sxs-lookup"><span data-stu-id="7e0d8-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="7e0d8-122">Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="7e0d8-123">Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="7e0d8-124">Definizione dei ruoli con i gruppi di sicurezza di Azure AD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="7e0d8-125">`groupMembershipClaims` attributo</span><span class="sxs-lookup"><span data-stu-id="7e0d8-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="7e0d8-126">Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* di AAD.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="7e0d8-127">La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="7e0d8-128">L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="7e0d8-129">Quando il numero di ruoli di amministratore e gruppi definiti dall'utente assegnati è superiore a cinque, AAD Invia un' `hasgroups` attestazione con un `true` valore anziché inviare un' `groups` attestazione.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="7e0d8-130">Qualsiasi app che può avere più di cinque ruoli e gruppi assegnati ai propri utenti deve effettuare una chiamata separata API Graph per ottenere i ruoli e i gruppi di un utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="7e0d8-131">L'implementazione di esempio disponibile in questo articolo illustra questo scenario.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="7e0d8-132">Per altre informazioni, vedere l' `groups` articolo relativo alle `hasgroups` informazioni sulle attestazioni e in [token di accesso della piattaforma Microsoft Identity: payload Claims](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="7e0d8-133">Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="7e0d8-134">Assegnare una matrice vuota a ogni proprietà in modo che il controllo `null` non sia necessario quando queste proprietà vengono usate nei cicli in un `foreach` secondo momento.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="7e0d8-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="7e0d8-136">Nell'app autonoma o nell' *`Client`* app di una soluzione ospitata Blazor creare una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-136">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="7e0d8-137">Utilizzare l'ambito corretto (autorizzazione) per le chiamate API Graph che ottengono informazioni sui ruoli e sui gruppi.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="7e0d8-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="7e0d8-139">In `Program.Main` ( `Program.cs` ) aggiungere il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio di implementazione e aggiungere un oggetto denominato <xref:System.Net.Http.HttpClient> per eseguire richieste di API Graph.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="7e0d8-140">Nell'esempio seguente viene denominato il client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="7e0d8-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="7e0d8-141">Creare classi di oggetti directory di AAD per ricevere i ruoli e i gruppi di Open Data Protocol (OData) da una chiamata di API Graph.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="7e0d8-142">OData arriva in formato JSON e una chiamata a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popola un'istanza della `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="7e0d8-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="7e0d8-144">Creare una factory utente personalizzata per elaborare le attestazioni dei ruoli e dei gruppi.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="7e0d8-145">Nell'implementazione di esempio seguente viene anche gestita la `roles` matrice di attestazioni, che viene analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="7e0d8-146">Se l' `hasgroups` attestazione è presente, il nome <xref:System.Net.Http.HttpClient> viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="7e0d8-147">Questa implementazione usa l'endpoint di Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentazione dell'API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="7e0d8-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="7e0d8-148">Le linee guida in questo argomento verranno aggiornate per Identity la versione 2.0 quando i pacchetti MSAL vengono aggiornati per la versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="7e0d8-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-149">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="7e0d8-150">Non è necessario fornire codice per rimuovere l' `groups` attestazione originale, se presente, perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="7e0d8-151">L'approccio in questo esempio:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="7e0d8-152">Aggiunge una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata per alleghire i token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="7e0d8-153">Aggiunge un oggetto denominato <xref:System.Net.Http.HttpClient> per l'esecuzione di richieste API Web a un endpoint sicuro dell'API Web esterno.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="7e0d8-154">Usa il denominato <xref:System.Net.Http.HttpClient> per eseguire richieste autorizzate.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="7e0d8-155">Il code coverage generale per questo approccio è disponibile in questo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> articolo.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="7e0d8-156">Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o *`Client`* dell'app di una Blazor soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="7e0d8-157">Consenso all' `Directory.Read.All` ambito di autorizzazione come ambito aggiuntivo per l'app:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="7e0d8-158">Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="7e0d8-159">L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* di AAD:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-159">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="7e0d8-160">Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-160">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="7e0d8-161">Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="7e0d8-162">Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="7e0d8-163">L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="7e0d8-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="7e0d8-164">Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="7e0d8-165">Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="7e0d8-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="7e0d8-166">Autorizzare l'accesso all'API server per i ruoli di amministratore e i gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="7e0d8-166">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="7e0d8-167">Oltre ad autorizzare gli utenti nell'app webassembly sul lato client ad accedere a pagine e risorse, l'API server può autorizzare gli utenti ad accedere agli endpoint API protetti.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-167">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="7e0d8-168">Dopo che l'app *Server* convalida il token di accesso dell'utente:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-168">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="7e0d8-169">L'app usa l'attestazione dell'identificatore di oggetto non modificabile dell'utente [( `oid` )](/azure/active-directory/develop/id-tokens#payload-claims) da JWT ( `id_token` ) per ottenere un token di accesso per API Graph.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-169">The app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from the JWT (`id_token`) to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="7e0d8-170">Una chiamata API Graph ottiene le appartenenze ai ruoli di amministratore e del gruppo di sicurezza definiti dall'utente di Azure dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-170">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships.</span></span>
* <span data-ttu-id="7e0d8-171">Le appartenenze vengono usate per stabilire le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-171">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="7e0d8-172">È possibile usare i [criteri di autorizzazione](xref:security/authorization/policies) per limitare l'accesso degli utenti agli endpoint dell'API server.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-172">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints.</span></span>

> [!NOTE]
> <span data-ttu-id="7e0d8-173">Questa guida attualmente non include l'autorizzazione degli utenti sulla base dei [ruoli definiti dall'utente di AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="7e0d8-173">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

### <a name="packages"></a><span data-ttu-id="7e0d8-174">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="7e0d8-174">Packages</span></span>

<span data-ttu-id="7e0d8-175">Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-175">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="7e0d8-176">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="7e0d8-176">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="7e0d8-177">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="7e0d8-177">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="7e0d8-178">Configurazione di Azure</span><span class="sxs-lookup"><span data-stu-id="7e0d8-178">Azure configuration</span></span>

* <span data-ttu-id="7e0d8-179">Verificare che alla registrazione dell'app *Server* sia concesso l'accesso all'API per l'autorizzazione API Graph per `Directory.Read.All` , che rappresenta il livello di accesso con privilegi minimi per i gruppi di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-179">Confirm that the *Server* app registration is given API access to the Graph API permission for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="7e0d8-180">Verificare che il consenso dell'amministratore venga applicato all'autorizzazione dopo aver eseguito l'assegnazione delle autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-180">Confirm that admin consent is applied to the permission after making the permission assignment.</span></span>
* <span data-ttu-id="7e0d8-181">Assegnare un nuovo segreto client all'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-181">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="7e0d8-182">Prendere nota del segreto per la configurazione dell'app nella sezione [impostazioni app](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-182">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="7e0d8-183">Impostazioni dell'app</span><span class="sxs-lookup"><span data-stu-id="7e0d8-183">App settings</span></span>

<span data-ttu-id="7e0d8-184">Nel file di impostazioni dell'app ( `appsettings.json` o `appsettings.Production.json` ) creare una `ClientSecret` voce con il segreto client dell'app *Server* dalla portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-184">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="7e0d8-185">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-185">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a><span data-ttu-id="7e0d8-186">I Criteri di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="7e0d8-186">Authorization policies</span></span>

<span data-ttu-id="7e0d8-187">Creare [criteri di autorizzazione](xref:security/authorization/policies) per i gruppi di sicurezza AAD e i ruoli di amministratore di AAD nell'app *Server* `Startup.ConfigureServices` ( `Startup.cs` ) basata sugli ID oggetto gruppo e gli [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="7e0d8-187">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="7e0d8-188">Un criterio del ruolo amministratore fatturazione di Azure, ad esempio, ha la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-188">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="7e0d8-189">Per altre informazioni, vedere <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-189">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="7e0d8-190">Accesso al controller</span><span class="sxs-lookup"><span data-stu-id="7e0d8-190">Controller access</span></span>

<span data-ttu-id="7e0d8-191">Richiedere criteri nei controller dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-191">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="7e0d8-192">Nell'esempio seguente viene limitato l'accesso ai dati di fatturazione da `BillingDataController` ad amministratori della fatturazione di Azure con un nome di criterio `BillingAdmin` , come configurato nella sezione [criteri di autorizzazione](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="7e0d8-192">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a><span data-ttu-id="7e0d8-193">Configurazione del servizio</span><span class="sxs-lookup"><span data-stu-id="7e0d8-193">Service configuration</span></span>

<span data-ttu-id="7e0d8-194">Nel metodo *Server* dell'app Server `Startup.ConfigureServices` aggiungere la logica per eseguire la chiamata API Graph e stabilire le `group` attestazioni utente per i gruppi di sicurezza e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-194">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="7e0d8-195">Il codice di esempio in questa sezione usa il Active Directory Authentication Library (ADAL), che è basato su Microsoft Identity Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-195">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span> <span data-ttu-id="7e0d8-196">Questo argomento verrà aggiornato per la Identity versione 2.0 per .NET 5.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-196">This topic will be updated for Identity v2.0 for .NET 5.</span></span> <span data-ttu-id="7e0d8-197">Tenere traccia dello stato di avanzamento del lavoro monitorando [[RC1] Microsoft Identity Platform 2,0 per Blazor (DotNet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span><span class="sxs-lookup"><span data-stu-id="7e0d8-197">Track progress on this work by monitoring [[RC1] Microsoft Identity Platform 2.0 for Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).</span></span>

<span data-ttu-id="7e0d8-198">Sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-198">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="7e0d8-199">Il set di `using` istruzioni seguente include gli spazi dei nomi richiesti per il codice seguente in questa sezione:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-199">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="7e0d8-200">Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="7e0d8-200">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="7e0d8-201">Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-201">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="7e0d8-202">Ad esempio, l'app può registrare l'autenticazione non riuscita.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-202">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="7e0d8-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-203">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="7e0d8-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-204"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="7e0d8-205">Attiva solo le informazioni personali per il debug con gli account utente di test.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-205">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="7e0d8-206">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-206">In `Startup.ConfigureServices`:</span></span>

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
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
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

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="7e0d8-207">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-207">In the preceding example:</span></span>

* <span data-ttu-id="7e0d8-208"><xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Viene effettuato un tentativo di acquisizione del token invisibile all'utente () perché il token di accesso potrebbe essere già stato archiviato nella cache dei token adal.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-208">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="7e0d8-209">È più veloce ottenere il token dalla cache anziché richiedere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-209">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="7e0d8-210">Se il token di accesso non viene acquisito dalla cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> viene generata), viene eseguita un'asserzione utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) con la credenziale client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) per ottenere il token per conto dell'utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="7e0d8-210">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="7e0d8-211">Successivamente, il `Microsoft.Graph.GraphServiceClient` può continuare a usare il token per effettuare la chiamata API Graph.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-211">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="7e0d8-212">Il token viene inserito nella cache dei token ADAL.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-212">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="7e0d8-213">Per API Graph future chiamate per lo stesso utente, il token viene acquisito dalla cache in modo invisibile all'utente con <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-213">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

<span data-ttu-id="7e0d8-214">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non ottiene le appartenenze transitive.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-214">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="7e0d8-215">Per modificare il codice per ottenere l'appartenenza diretta e transitiva:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-215">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="7e0d8-216">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-216">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="7e0d8-217">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-217">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="7e0d8-218">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-218">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="7e0d8-219">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-219">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="7e0d8-220">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non distingue tra i gruppi di sicurezza AAD e i ruoli di amministratore di AAD durante la creazione di attestazioni.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-220">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="7e0d8-221">Per fare in modo che l'app distingua tra gruppi e ruoli, controllare `entry.ODataType` quando si scorrono i gruppi e i ruoli.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-221">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="7e0d8-222">Per creare attestazioni di ruolo e gruppi di sicurezza separati, usare codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-222">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="7e0d8-223">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="7e0d8-223">User-defined roles</span></span>

<span data-ttu-id="7e0d8-224">È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-224">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="7e0d8-225">Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-225">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="7e0d8-226">Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-226">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="7e0d8-227">Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-227">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="7e0d8-228">Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-228">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="7e0d8-229">Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-229">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="7e0d8-230">La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-230">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="7e0d8-231">L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-231">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="7e0d8-232">Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-232">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="7e0d8-233">Aggiungere e registrare `CustomUserFactory` nell'app autonoma o in *`Client`* un'app di una Blazor soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-233">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="7e0d8-234">Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-234">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="7e0d8-235">Nell'app `Program.Main` autonoma o *`Client`* app di una soluzione ospitata Blazor specificare l'attestazione denominata " `role` " come attestazione del ruolo:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-235">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="7e0d8-236">A questo punto sono funzionali gli approcci di autorizzazione per i componenti.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-236">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="7e0d8-237">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-237">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="7e0d8-238">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="7e0d8-238">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="7e0d8-239">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="7e0d8-239">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="7e0d8-240">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="7e0d8-240">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="7e0d8-241">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="7e0d8-241">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="7e0d8-242">ID oggetto ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-242">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="7e0d8-243">Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-243">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="7e0d8-244">I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-244">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="7e0d8-245">Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="7e0d8-245">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="7e0d8-246">Ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="7e0d8-246">AAD Administrator Role</span></span> | <span data-ttu-id="7e0d8-247">ID dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="7e0d8-247">Object ID</span></span>
--- | ---
<span data-ttu-id="7e0d8-248">Amministratore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="7e0d8-248">Application administrator</span></span> | <span data-ttu-id="7e0d8-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="7e0d8-249">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="7e0d8-250">Sviluppatore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="7e0d8-250">Application developer</span></span> | <span data-ttu-id="7e0d8-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="7e0d8-251">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="7e0d8-252">Amministratore dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="7e0d8-252">Authentication administrator</span></span> | <span data-ttu-id="7e0d8-253">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="7e0d8-253">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="7e0d8-254">Amministratore di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="7e0d8-254">Azure DevOps administrator</span></span> | <span data-ttu-id="7e0d8-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="7e0d8-255">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="7e0d8-256">Amministratore di Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="7e0d8-256">Azure Information Protection administrator</span></span> | <span data-ttu-id="7e0d8-257">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="7e0d8-257">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="7e0d8-258">Amministratore keyset Framework dell'esperienza B2C</span><span class="sxs-lookup"><span data-stu-id="7e0d8-258">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="7e0d8-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="7e0d8-259">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="7e0d8-260">Amministratore criteri B2C Framework dell'esperienza</span><span class="sxs-lookup"><span data-stu-id="7e0d8-260">B2C IEF Policy administrator</span></span> | <span data-ttu-id="7e0d8-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="7e0d8-261">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="7e0d8-262">Amministratore flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="7e0d8-262">B2C user flow administrator</span></span> | <span data-ttu-id="7e0d8-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="7e0d8-263">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="7e0d8-264">Amministratore attributi flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="7e0d8-264">B2C user flow attribute administrator</span></span> | <span data-ttu-id="7e0d8-265">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="7e0d8-265">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="7e0d8-266">Amministratore fatturazione</span><span class="sxs-lookup"><span data-stu-id="7e0d8-266">Billing administrator</span></span> | <span data-ttu-id="7e0d8-267">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="7e0d8-267">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="7e0d8-268">Amministratore di applicazioni cloud</span><span class="sxs-lookup"><span data-stu-id="7e0d8-268">Cloud application administrator</span></span> | <span data-ttu-id="7e0d8-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="7e0d8-269">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="7e0d8-270">Amministratore dispositivo cloud</span><span class="sxs-lookup"><span data-stu-id="7e0d8-270">Cloud device administrator</span></span> | <span data-ttu-id="7e0d8-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="7e0d8-271">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="7e0d8-272">Amministratore di conformità</span><span class="sxs-lookup"><span data-stu-id="7e0d8-272">Compliance administrator</span></span> | <span data-ttu-id="7e0d8-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="7e0d8-273">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="7e0d8-274">Amministratore dei dati sulla conformità</span><span class="sxs-lookup"><span data-stu-id="7e0d8-274">Compliance data administrator</span></span> | <span data-ttu-id="7e0d8-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="7e0d8-275">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="7e0d8-276">Amministratore di accesso condizionale</span><span class="sxs-lookup"><span data-stu-id="7e0d8-276">Conditional Access administrator</span></span> | <span data-ttu-id="7e0d8-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="7e0d8-277">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="7e0d8-278">Responsabile approvazione per l'accesso a Customer Lockbox</span><span class="sxs-lookup"><span data-stu-id="7e0d8-278">Customer LockBox access approver</span></span> | <span data-ttu-id="7e0d8-279">c18d54a8-B13E-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="7e0d8-279">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="7e0d8-280">Amministratore di analisi desktop</span><span class="sxs-lookup"><span data-stu-id="7e0d8-280">Desktop Analytics administrator</span></span> | <span data-ttu-id="7e0d8-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="7e0d8-281">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="7e0d8-282">Ruoli con autorizzazioni di lettura nella directory</span><span class="sxs-lookup"><span data-stu-id="7e0d8-282">Directory readers</span></span> | <span data-ttu-id="7e0d8-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="7e0d8-283">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="7e0d8-284">Amministratore di Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="7e0d8-284">Dynamics 365 administrator</span></span> | <span data-ttu-id="7e0d8-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="7e0d8-285">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="7e0d8-286">Amministratori di Exchange</span><span class="sxs-lookup"><span data-stu-id="7e0d8-286">Exchange administrator</span></span> | <span data-ttu-id="7e0d8-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="7e0d8-287">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="7e0d8-288">IdentityAmministratore provider esterno</span><span class="sxs-lookup"><span data-stu-id="7e0d8-288">External Identity Provider administrator</span></span> | <span data-ttu-id="7e0d8-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="7e0d8-289">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="7e0d8-290">Amministratore globale</span><span class="sxs-lookup"><span data-stu-id="7e0d8-290">Global administrator</span></span> | <span data-ttu-id="7e0d8-291">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="7e0d8-291">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="7e0d8-292">Ruolo con autorizzazioni di lettura globali</span><span class="sxs-lookup"><span data-stu-id="7e0d8-292">Global reader</span></span> | <span data-ttu-id="7e0d8-293">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="7e0d8-293">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="7e0d8-294">Amministratore di gruppi</span><span class="sxs-lookup"><span data-stu-id="7e0d8-294">Groups administrator</span></span> | <span data-ttu-id="7e0d8-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="7e0d8-295">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="7e0d8-296">Mittente dell'invito guest</span><span class="sxs-lookup"><span data-stu-id="7e0d8-296">Guest inviter</span></span> | <span data-ttu-id="7e0d8-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="7e0d8-297">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="7e0d8-298">Amministratore dell'help desk</span><span class="sxs-lookup"><span data-stu-id="7e0d8-298">Helpdesk administrator</span></span> | <span data-ttu-id="7e0d8-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="7e0d8-299">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="7e0d8-300">Amministratore di Intune</span><span class="sxs-lookup"><span data-stu-id="7e0d8-300">Intune administrator</span></span> | <span data-ttu-id="7e0d8-301">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="7e0d8-301">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="7e0d8-302">Amministratore di Kaizala</span><span class="sxs-lookup"><span data-stu-id="7e0d8-302">Kaizala administrator</span></span> | <span data-ttu-id="7e0d8-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="7e0d8-303">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="7e0d8-304">Amministratore licenze</span><span class="sxs-lookup"><span data-stu-id="7e0d8-304">License administrator</span></span> | <span data-ttu-id="7e0d8-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="7e0d8-305">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="7e0d8-306">Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="7e0d8-306">Message center privacy reader</span></span> | <span data-ttu-id="7e0d8-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="7e0d8-307">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="7e0d8-308">Ruolo con autorizzazioni di lettura per il Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="7e0d8-308">Message center reader</span></span> | <span data-ttu-id="7e0d8-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="7e0d8-309">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="7e0d8-310">Amministratore delle app di Office</span><span class="sxs-lookup"><span data-stu-id="7e0d8-310">Office apps administrator</span></span> | <span data-ttu-id="7e0d8-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="7e0d8-311">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="7e0d8-312">Amministratore password</span><span class="sxs-lookup"><span data-stu-id="7e0d8-312">Password administrator</span></span> | <span data-ttu-id="7e0d8-313">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="7e0d8-313">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="7e0d8-314">Amministratore di Power BI</span><span class="sxs-lookup"><span data-stu-id="7e0d8-314">Power BI administrator</span></span> | <span data-ttu-id="7e0d8-315">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="7e0d8-315">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="7e0d8-316">Amministratore di Power Platform</span><span class="sxs-lookup"><span data-stu-id="7e0d8-316">Power platform administrator</span></span> | <span data-ttu-id="7e0d8-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="7e0d8-317">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="7e0d8-318">Amministratore autenticazione con privilegi</span><span class="sxs-lookup"><span data-stu-id="7e0d8-318">Privileged authentication administrator</span></span> | <span data-ttu-id="7e0d8-319">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="7e0d8-319">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="7e0d8-320">Amministratore dei ruoli con privilegi</span><span class="sxs-lookup"><span data-stu-id="7e0d8-320">Privileged role administrator</span></span> | <span data-ttu-id="7e0d8-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="7e0d8-321">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="7e0d8-322">Lettore di report</span><span class="sxs-lookup"><span data-stu-id="7e0d8-322">Reports reader</span></span> | <span data-ttu-id="7e0d8-323">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="7e0d8-323">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="7e0d8-324">Amministratore della ricerca</span><span class="sxs-lookup"><span data-stu-id="7e0d8-324">Search administrator</span></span> | <span data-ttu-id="7e0d8-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="7e0d8-325">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="7e0d8-326">Editor della ricerca</span><span class="sxs-lookup"><span data-stu-id="7e0d8-326">Search editor</span></span> | <span data-ttu-id="7e0d8-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="7e0d8-327">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="7e0d8-328">Amministratore della sicurezza</span><span class="sxs-lookup"><span data-stu-id="7e0d8-328">Security administrator</span></span> | <span data-ttu-id="7e0d8-329">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="7e0d8-329">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="7e0d8-330">Operatore per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="7e0d8-330">Security operator</span></span> | <span data-ttu-id="7e0d8-331">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="7e0d8-331">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="7e0d8-332">Ruolo con autorizzazioni di lettura per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="7e0d8-332">Security reader</span></span> | <span data-ttu-id="7e0d8-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="7e0d8-333">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="7e0d8-334">Amministratore del supporto per il servizio</span><span class="sxs-lookup"><span data-stu-id="7e0d8-334">Service support administrator</span></span> | <span data-ttu-id="7e0d8-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="7e0d8-335">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="7e0d8-336">Amministratore di SharePoint</span><span class="sxs-lookup"><span data-stu-id="7e0d8-336">SharePoint administrator</span></span> | <span data-ttu-id="7e0d8-337">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="7e0d8-337">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="7e0d8-338">Amministratore di Skype for Business</span><span class="sxs-lookup"><span data-stu-id="7e0d8-338">Skype for Business administrator</span></span> | <span data-ttu-id="7e0d8-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="7e0d8-339">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="7e0d8-340">Amministratore comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="7e0d8-340">Teams Communications Administrator</span></span> | <span data-ttu-id="7e0d8-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="7e0d8-341">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="7e0d8-342">Tecnico di supporto comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="7e0d8-342">Teams Communications Support Engineer</span></span> | <span data-ttu-id="7e0d8-343">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="7e0d8-343">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="7e0d8-344">Specialista comunicazione Teams</span><span class="sxs-lookup"><span data-stu-id="7e0d8-344">Teams Communications Specialist</span></span> | <span data-ttu-id="7e0d8-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="7e0d8-345">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="7e0d8-346">Amministratore del servizio Teams</span><span class="sxs-lookup"><span data-stu-id="7e0d8-346">Teams Service Administrator</span></span> | <span data-ttu-id="7e0d8-347">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="7e0d8-347">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="7e0d8-348">Amministratore utenti</span><span class="sxs-lookup"><span data-stu-id="7e0d8-348">User administrator</span></span> | <span data-ttu-id="7e0d8-349">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="7e0d8-349">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7e0d8-350">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7e0d8-350">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
