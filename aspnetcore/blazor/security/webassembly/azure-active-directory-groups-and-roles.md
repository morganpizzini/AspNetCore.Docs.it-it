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
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058259"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="e451e-103">Gruppi di Azure Active Directory (AAD), ruoli di amministratore e ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="e451e-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="e451e-104">Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e451e-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="e451e-105">Questo articolo si applica alle Blazor app ASP.NET Core versione 3,1 con Microsoft Identity 1,0 e verrà aggiornato a 5,0 con Identity 2,0 a breve.</span><span class="sxs-lookup"><span data-stu-id="e451e-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="e451e-106">Per altre informazioni, vedere il problema e la richiesta pull seguenti di GitHub.</span><span class="sxs-lookup"><span data-stu-id="e451e-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="e451e-107">La scheda **file modificati** della richiesta pull contiene il testo bozza ed esempi per gli aggiornamenti all'articolo.</span><span class="sxs-lookup"><span data-stu-id="e451e-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="e451e-108">Dopo la revisione e gli aggiornamenti finali, la richiesta pull verrà unita al set di documentazione in tempo reale.</span><span class="sxs-lookup"><span data-stu-id="e451e-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="e451e-109">Problema: [ Blazor WASM con i gruppi e i ruoli di AAD (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="e451e-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="e451e-110">Richiesta pull: [ Blazor argomenti dei gruppi e dei ruoli di AAD 5,0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="e451e-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="e451e-111">Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="e451e-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="e451e-112">Gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="e451e-112">User-defined groups</span></span>
  * <span data-ttu-id="e451e-113">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="e451e-113">Security</span></span>
  * <span data-ttu-id="e451e-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="e451e-114">Microsoft 365</span></span>
  * <span data-ttu-id="e451e-115">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="e451e-115">Distribution</span></span>
* <span data-ttu-id="e451e-116">Ruoli</span><span class="sxs-lookup"><span data-stu-id="e451e-116">Roles</span></span>
  * <span data-ttu-id="e451e-117">Ruoli di amministratore di AAD</span><span class="sxs-lookup"><span data-stu-id="e451e-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="e451e-118">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="e451e-118">User-defined roles</span></span>

<span data-ttu-id="e451e-119">Le indicazioni fornite in questo articolo sono valide per gli Blazor WebAssembly scenari di distribuzione di AAD descritti negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e451e-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="e451e-120">App autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="e451e-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="e451e-121">App autonoma con AAD</span><span class="sxs-lookup"><span data-stu-id="e451e-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="e451e-122">App ospitata con AAD</span><span class="sxs-lookup"><span data-stu-id="e451e-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="e451e-123">Ambiti</span><span class="sxs-lookup"><span data-stu-id="e451e-123">Scopes</span></span>

<span data-ttu-id="e451e-124">È necessaria una chiamata [API Microsoft Graph](/graph/use-the-api) per qualsiasi utente dell'app con più di cinque appartenenze ai gruppi di sicurezza e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="e451e-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="e451e-125">Per consentire le chiamate di API Graph, assegnare all'app autonoma o *`Client`* di una soluzione ospitata una Blazor delle seguenti [autorizzazioni di API Graph (ambiti)](/graph/permissions-reference) nel portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="e451e-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="e451e-126">`Directory.Read.All` è l'ambito con privilegi minimi e è l'ambito usato per l'esempio descritto in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="e451e-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="e451e-127">Gruppi definiti dall'utente e ruoli di amministratore</span><span class="sxs-lookup"><span data-stu-id="e451e-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="e451e-128">Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure.</span><span class="sxs-lookup"><span data-stu-id="e451e-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="e451e-129">Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="e451e-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="e451e-130">Definizione dei ruoli con i gruppi di sicurezza di Azure AD</span><span class="sxs-lookup"><span data-stu-id="e451e-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="e451e-131">`groupMembershipClaims` attributo</span><span class="sxs-lookup"><span data-stu-id="e451e-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="e451e-132">Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* di AAD.</span><span class="sxs-lookup"><span data-stu-id="e451e-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="e451e-133">La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="e451e-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="e451e-134">L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="e451e-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="e451e-135">Quando il numero di ruoli di amministratore e gruppi definiti dall'utente assegnati è superiore a cinque, AAD Invia un' `hasgroups` attestazione con un `true` valore anziché inviare un' `groups` attestazione.</span><span class="sxs-lookup"><span data-stu-id="e451e-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="e451e-136">Qualsiasi app che può avere più di cinque ruoli e gruppi assegnati ai propri utenti deve effettuare una chiamata separata API Graph per ottenere i ruoli e i gruppi di un utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="e451e-137">L'implementazione di esempio disponibile in questo articolo illustra questo scenario.</span><span class="sxs-lookup"><span data-stu-id="e451e-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="e451e-138">Per altre informazioni, vedere l' `groups` articolo relativo alle `hasgroups` informazioni sulle attestazioni e in [token di accesso della piattaforma Microsoft Identity: payload Claims](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="e451e-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="e451e-139">Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.</span><span class="sxs-lookup"><span data-stu-id="e451e-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="e451e-140">Assegnare una matrice vuota a ogni proprietà in modo che il controllo `null` non sia necessario quando queste proprietà vengono usate nei cicli in un `foreach` secondo momento.</span><span class="sxs-lookup"><span data-stu-id="e451e-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="e451e-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="e451e-141">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="e451e-142">Usare **uno** degli approcci seguenti per creare attestazioni per i gruppi e i ruoli di AAD:</span><span class="sxs-lookup"><span data-stu-id="e451e-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="e451e-143">Usare Graph SDK</span><span class="sxs-lookup"><span data-stu-id="e451e-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="e451e-144">Usa un oggetto denominato `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="e451e-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="e451e-145">Usare Graph SDK</span><span class="sxs-lookup"><span data-stu-id="e451e-145">Use the Graph SDK</span></span>

<span data-ttu-id="e451e-146">Aggiungere un riferimento al pacchetto all'app autonoma o all' *`Client`* app di una soluzione ospitata Blazor per [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="e451e-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="e451e-147">Aggiungere le classi e la configurazione dell'utilità Graph SDK nella sezione *Graph SDK* dell' <xref:blazor/security/webassembly/graph-api#graph-sdk> articolo.</span><span class="sxs-lookup"><span data-stu-id="e451e-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="e451e-148">Aggiungere la factory di account utente personalizzata seguente alla APPO autonoma o all' *`Client`* app di una soluzione ospitata Blazor ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="e451e-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="e451e-149">La Factory utente personalizzata viene usata per elaborare le attestazioni di ruoli e gruppi.</span><span class="sxs-lookup"><span data-stu-id="e451e-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="e451e-150">La `roles` matrice di attestazioni è analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="e451e-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="e451e-151">Se l' `hasgroups` attestazione è presente, Graph SDK viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente:</span><span class="sxs-lookup"><span data-stu-id="e451e-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

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

<span data-ttu-id="e451e-152">Il codice precedente non include le appartenenze transitive.</span><span class="sxs-lookup"><span data-stu-id="e451e-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="e451e-153">Se l'app richiede attestazioni di appartenenza a gruppi dirette e transitive:</span><span class="sxs-lookup"><span data-stu-id="e451e-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="e451e-154">Modificare il `IUserMemberOfCollectionWithReferencesPage` tipo per `groupsAndAzureRoles` in `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="e451e-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="e451e-155">Quando si richiedono i gruppi e i ruoli dell'utente, sostituire la `MemberOf` proprietà con `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="e451e-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="e451e-156">In `Program.Main` ( `Program.cs` ) configurare l'autenticazione MSAL per l'uso della factory dell'account utente personalizzata: se l'app usa una classe di account utente personalizzata che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , scambiare la classe di account utente personalizzata per <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e451e-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="e451e-157">Usa un oggetto denominato `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="e451e-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="e451e-158">Nell'app autonoma o nell' *`Client`* app di una soluzione ospitata Blazor creare una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="e451e-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="e451e-159">Utilizzare l'ambito corretto per API Graph le chiamate che ottengono informazioni sui ruoli e sui gruppi.</span><span class="sxs-lookup"><span data-stu-id="e451e-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="e451e-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="e451e-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="e451e-161">In `Program.Main` ( `Program.cs` ) aggiungere il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio di implementazione e aggiungere un oggetto denominato <xref:System.Net.Http.HttpClient> per eseguire richieste di API Graph.</span><span class="sxs-lookup"><span data-stu-id="e451e-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="e451e-162">Nell'esempio seguente viene denominato il client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="e451e-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="e451e-163">Creare classi di oggetti directory di AAD per ricevere i ruoli e i gruppi di Open Data Protocol (OData) da una chiamata di API Graph.</span><span class="sxs-lookup"><span data-stu-id="e451e-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="e451e-164">OData arriva in formato JSON e una chiamata a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popola un'istanza della `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="e451e-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="e451e-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="e451e-165">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="e451e-166">Creare una factory utente personalizzata per elaborare le attestazioni dei ruoli e dei gruppi.</span><span class="sxs-lookup"><span data-stu-id="e451e-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="e451e-167">Nell'implementazione di esempio seguente viene anche gestita la `roles` matrice di attestazioni, che viene analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="e451e-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="e451e-168">Se l' `hasgroups` attestazione è presente, il nome <xref:System.Net.Http.HttpClient> viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="e451e-169">Questa implementazione usa l'endpoint di Microsoft Identity Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentazione dell'API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="e451e-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="e451e-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="e451e-170">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="e451e-171">Non è necessario fornire codice per rimuovere l' `groups` attestazione originale, se presente, perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="e451e-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="e451e-172">L'approccio in questo esempio:</span><span class="sxs-lookup"><span data-stu-id="e451e-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="e451e-173">Aggiunge una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata per alleghire i token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="e451e-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="e451e-174">Aggiunge un oggetto denominato <xref:System.Net.Http.HttpClient> per l'esecuzione di richieste API Web a un endpoint sicuro dell'API Web esterno.</span><span class="sxs-lookup"><span data-stu-id="e451e-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="e451e-175">Usa il denominato <xref:System.Net.Http.HttpClient> per eseguire richieste autorizzate.</span><span class="sxs-lookup"><span data-stu-id="e451e-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="e451e-176">Il code coverage generale per questo approccio è disponibile in questo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> articolo.</span><span class="sxs-lookup"><span data-stu-id="e451e-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="e451e-177">Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o *`Client`* dell'app di una Blazor soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="e451e-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="e451e-178">Consenso all' `Directory.Read.All` ambito come ambito aggiuntivo per l'app:</span><span class="sxs-lookup"><span data-stu-id="e451e-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="e451e-179">Configurazione dell'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e451e-179">Authorization configuration</span></span>

<span data-ttu-id="e451e-180">Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="e451e-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="e451e-181">L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* di AAD:</span><span class="sxs-lookup"><span data-stu-id="e451e-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="e451e-182">Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="e451e-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="e451e-183">Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="e451e-184">Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:</span><span class="sxs-lookup"><span data-stu-id="e451e-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="e451e-185">L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="e451e-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="e451e-186">Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.</span><span class="sxs-lookup"><span data-stu-id="e451e-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="e451e-187">Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="e451e-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="e451e-188">Autorizzare l'accesso all'API server per i ruoli di amministratore e i gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="e451e-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="e451e-189">Oltre ad autorizzare gli utenti nell'app webassembly sul lato client ad accedere a pagine e risorse, l'API server può autorizzare gli utenti ad accedere agli endpoint API protetti.</span><span class="sxs-lookup"><span data-stu-id="e451e-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="e451e-190">Dopo che l'app *Server* convalida il token di accesso dell'utente:</span><span class="sxs-lookup"><span data-stu-id="e451e-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="e451e-191">L'app per le API server usa l' [attestazione dell'identificatore di oggetto `oid` ](/azure/active-directory/develop/id-tokens#payload-claims) non modificabile dell'utente () dal relativo token di accesso per ottenere un token di accesso per API Graph.</span><span class="sxs-lookup"><span data-stu-id="e451e-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="e451e-192">Una chiamata API Graph ottiene le appartenenze ai ruoli di amministratore e del gruppo di sicurezza definiti dall'utente di Azure dell'utente chiamando [`memberOf`](/graph/api/user-list-memberof) sull'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="e451e-193">Le appartenenze vengono usate per stabilire le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="e451e-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="e451e-194">È possibile usare i [criteri di autorizzazione](xref:security/authorization/policies) per limitare l'accesso degli utenti agli endpoint dell'API server nell'intera app.</span><span class="sxs-lookup"><span data-stu-id="e451e-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e451e-195">Questa guida attualmente non include l'autorizzazione degli utenti sulla base dei [ruoli definiti dall'utente di AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="e451e-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="e451e-196">Le indicazioni fornite in questa sezione configurano l'app per le API server come [*app daemon*](/azure/active-directory/develop/scenario-daemon-overview) per la chiamata API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="e451e-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="e451e-197">Questo approccio **non**:</span><span class="sxs-lookup"><span data-stu-id="e451e-197">This approach does **not**:</span></span>

* <span data-ttu-id="e451e-198">Richiedere l' `access_as_user` ambito.</span><span class="sxs-lookup"><span data-stu-id="e451e-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="e451e-199">Accedere API Graph per conto dell'utente o del client che effettua la richiesta dell'API.</span><span class="sxs-lookup"><span data-stu-id="e451e-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="e451e-200">La chiamata a API Graph dall'app per le API server richiede solo un' **applicazione** per l'app per le api Server API Graph ambito per `Directory.Read.All` nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="e451e-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="e451e-201">Questo approccio impedisce assolutamente all'app client di accedere ai dati della directory non consentiti in modo esplicito dall'API del server.</span><span class="sxs-lookup"><span data-stu-id="e451e-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="e451e-202">L'app client può accedere solo agli endpoint controller dell'app per le API del server.</span><span class="sxs-lookup"><span data-stu-id="e451e-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="e451e-203">Configurazione di Azure</span><span class="sxs-lookup"><span data-stu-id="e451e-203">Azure configuration</span></span>

* <span data-ttu-id="e451e-204">Verificare che alla registrazione dell'app *Server* venga assegnata l' **applicazione** (non **delegata**) API Graph ambito per `Directory.Read.All` , che rappresenta il livello di accesso con privilegi minimi per i gruppi di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="e451e-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="e451e-205">Verificare che l'autorizzazione dell'amministratore venga applicata all'ambito dopo aver eseguito l'assegnazione dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="e451e-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="e451e-206">Assegnare un nuovo segreto client all'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="e451e-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="e451e-207">Prendere nota del segreto per la configurazione dell'app nella sezione [impostazioni app](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="e451e-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="e451e-208">Impostazioni app</span><span class="sxs-lookup"><span data-stu-id="e451e-208">App settings</span></span>

<span data-ttu-id="e451e-209">Nel file di impostazioni dell'app ( `appsettings.json` o `appsettings.Production.json` ) creare una `ClientSecret` voce con il segreto client dell'app *Server* dalla portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="e451e-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="e451e-210">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e451e-210">For example:</span></span>

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
> <span data-ttu-id="e451e-211">Se il dominio dell'editore tenant non viene verificato, l'ambito dell'API server per l'accesso utente/client usa un `https://` URI basato su.</span><span class="sxs-lookup"><span data-stu-id="e451e-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="e451e-212">In questo scenario l'app per le API server richiede la `Audience` configurazione nel `appsettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="e451e-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="e451e-213">Nella configurazione seguente, la fine del valore non `Audience` include l'  ambito predefinito `/{DEFAULT SCOPE}` , in cui il segnaposto `{DEFAULT SCOPE}` è l'ambito predefinito:</span><span class="sxs-lookup"><span data-stu-id="e451e-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
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
> <span data-ttu-id="e451e-214">Nella configurazione dell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="e451e-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="e451e-215">Il dominio del tenant è `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="e451e-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="e451e-216">L'app per le API del server `ClientId` è `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="e451e-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="e451e-217">`Audience` **Non** è in genere necessario configurare un oggetto in modo esplicito per le app con un dominio Publisher verificato con `api://` ambito API basato su.</span><span class="sxs-lookup"><span data-stu-id="e451e-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="e451e-218">Per altre informazioni, vedere <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="e451e-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="e451e-219">I Criteri di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="e451e-219">Authorization policies</span></span>

<span data-ttu-id="e451e-220">Creare [criteri di autorizzazione](xref:security/authorization/policies) per i gruppi di sicurezza AAD e i ruoli di amministratore di AAD nell'app *Server* `Startup.ConfigureServices` ( `Startup.cs` ) basata sugli ID oggetto gruppo e gli [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="e451e-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="e451e-221">Un criterio del ruolo amministratore fatturazione di Azure, ad esempio, ha la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="e451e-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="e451e-222">Per altre informazioni, vedere <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="e451e-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="e451e-223">Accesso al controller</span><span class="sxs-lookup"><span data-stu-id="e451e-223">Controller access</span></span>

<span data-ttu-id="e451e-224">Richiedere criteri nei controller dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="e451e-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="e451e-225">Nell'esempio seguente viene limitato l'accesso ai dati di fatturazione da `BillingDataController` ad amministratori della fatturazione di Azure con un nome di criterio `BillingAdmin` , come configurato nella sezione [criteri di autorizzazione](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="e451e-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

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

### <a name="packages"></a><span data-ttu-id="e451e-226">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="e451e-226">Packages</span></span>

<span data-ttu-id="e451e-227">Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e451e-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="e451e-228">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="e451e-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="e451e-229">[Microsoft. Identity . Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="e451e-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="e451e-230">Servizi</span><span class="sxs-lookup"><span data-stu-id="e451e-230">Services</span></span>

<span data-ttu-id="e451e-231">Nel metodo  dell'app Server `Startup.ConfigureServices` sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="e451e-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="e451e-232">Aggiungere gli spazi dei nomi seguenti a `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="e451e-232">Add the following namespaces to `Startup.cs`:</span></span>

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

<span data-ttu-id="e451e-233">Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="e451e-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="e451e-234">Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e451e-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e451e-235">Ad esempio, l'app può registrare l'autenticazione non riuscita.</span><span class="sxs-lookup"><span data-stu-id="e451e-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="e451e-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="e451e-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="e451e-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="e451e-238">Attiva solo le informazioni personali per il debug con gli account utente di test.</span><span class="sxs-lookup"><span data-stu-id="e451e-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="e451e-239">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e451e-239">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e451e-240">Nel codice precedente, la gestione degli errori di token seguenti è facoltativa:</span><span class="sxs-lookup"><span data-stu-id="e451e-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="e451e-241">`MsalUiRequiredException`: L'app non ha autorizzazioni sufficienti (ambiti).</span><span class="sxs-lookup"><span data-stu-id="e451e-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="e451e-242">Determinare se gli ambiti delle app per le API del server nel portale di Azure includono un'autorizzazione per l' **applicazione** `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="e451e-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="e451e-243">Verificare che l'amministratore tenant abbia concesso le autorizzazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="e451e-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="e451e-244">`MsalServiceException` ( `AADSTS70011` ): Verificare che l'ambito sia `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="e451e-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="e451e-245">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="e451e-245">Packages</span></span>

<span data-ttu-id="e451e-246">Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="e451e-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="e451e-247">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="e451e-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="e451e-248">[Microsoft. Identity Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="e451e-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="e451e-249">Configurazione del servizio</span><span class="sxs-lookup"><span data-stu-id="e451e-249">Service configuration</span></span>

<span data-ttu-id="e451e-250">Nel metodo  dell'app Server `Startup.ConfigureServices` aggiungere la logica per eseguire la chiamata API Graph e stabilire le `group` attestazioni utente per i gruppi di sicurezza e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="e451e-251">Il codice di esempio in questa sezione usa il Active Directory Authentication Library (ADAL), che è basato su Microsoft Identity Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="e451e-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="e451e-252">Sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="e451e-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="e451e-253">Il set di `using` istruzioni seguente include gli spazi dei nomi richiesti per il codice seguente in questa sezione:</span><span class="sxs-lookup"><span data-stu-id="e451e-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="e451e-254">Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="e451e-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="e451e-255">Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="e451e-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e451e-256">Ad esempio, l'app può registrare l'autenticazione non riuscita.</span><span class="sxs-lookup"><span data-stu-id="e451e-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="e451e-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="e451e-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="e451e-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="e451e-259">Attiva solo le informazioni personali per il debug con gli account utente di test.</span><span class="sxs-lookup"><span data-stu-id="e451e-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="e451e-260">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e451e-260">In `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e451e-261">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="e451e-261">In the preceding example:</span></span>

* <span data-ttu-id="e451e-262"><xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Viene effettuato un tentativo di acquisizione del token invisibile all'utente () perché il token di accesso potrebbe essere già stato archiviato nella cache dei token adal.</span><span class="sxs-lookup"><span data-stu-id="e451e-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="e451e-263">È più veloce ottenere il token dalla cache anziché richiedere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="e451e-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="e451e-264">Se il token di accesso non viene acquisito dalla cache ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> viene generata), viene eseguita un'asserzione utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion> ) con la credenziale client ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential> ) per ottenere il token per conto dell'utente ( <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="e451e-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="e451e-265">Successivamente, il `Microsoft.Graph.GraphServiceClient` può continuare a usare il token per effettuare la chiamata API Graph.</span><span class="sxs-lookup"><span data-stu-id="e451e-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="e451e-266">Il token viene inserito nella cache dei token ADAL.</span><span class="sxs-lookup"><span data-stu-id="e451e-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="e451e-267">Per API Graph future chiamate per lo stesso utente, il token viene acquisito dalla cache in modo invisibile all'utente con <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="e451e-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="e451e-268">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non ottiene le appartenenze transitive.</span><span class="sxs-lookup"><span data-stu-id="e451e-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="e451e-269">Per modificare il codice per ottenere l'appartenenza diretta e transitiva:</span><span class="sxs-lookup"><span data-stu-id="e451e-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="e451e-270">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="e451e-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="e451e-271">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="e451e-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="e451e-272">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="e451e-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="e451e-273">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="e451e-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="e451e-274">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non distingue tra i gruppi di sicurezza AAD e i ruoli di amministratore di AAD durante la creazione di attestazioni.</span><span class="sxs-lookup"><span data-stu-id="e451e-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="e451e-275">Per fare in modo che l'app distingua tra gruppi e ruoli, controllare `entry.ODataType` quando si scorrono i gruppi e i ruoli.</span><span class="sxs-lookup"><span data-stu-id="e451e-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="e451e-276">Per creare attestazioni di ruolo e gruppi di sicurezza separati, usare codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="e451e-276">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="e451e-277">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="e451e-277">User-defined roles</span></span>

<span data-ttu-id="e451e-278">È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="e451e-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="e451e-279">Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="e451e-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="e451e-280">Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:</span><span class="sxs-lookup"><span data-stu-id="e451e-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="e451e-281">Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard.</span><span class="sxs-lookup"><span data-stu-id="e451e-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="e451e-282">Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="e451e-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="e451e-283">Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="e451e-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="e451e-284">La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="e451e-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="e451e-285">L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="e451e-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="e451e-286">Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="e451e-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="e451e-287">Aggiungere e registrare `CustomUserFactory` nell'app autonoma o in *`Client`* un'app di una Blazor soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="e451e-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="e451e-288">Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="e451e-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="e451e-289">Nell'app `Program.Main` autonoma o *`Client`* app di una soluzione ospitata Blazor specificare l'attestazione denominata " `role` " come attestazione del ruolo:</span><span class="sxs-lookup"><span data-stu-id="e451e-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="e451e-290">A questo punto sono funzionali gli approcci di autorizzazione per i componenti.</span><span class="sxs-lookup"><span data-stu-id="e451e-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="e451e-291">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="e451e-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="e451e-292">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="e451e-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="e451e-293">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="e451e-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="e451e-294">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="e451e-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="e451e-295">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="e451e-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="e451e-296">ID oggetto ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="e451e-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="e451e-297">Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="e451e-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="e451e-298">I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app.</span><span class="sxs-lookup"><span data-stu-id="e451e-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="e451e-299">Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="e451e-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="e451e-300">Ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="e451e-300">AAD Administrator Role</span></span> | <span data-ttu-id="e451e-301">ID dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="e451e-301">Object ID</span></span>
--- | ---
<span data-ttu-id="e451e-302">Amministratore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="e451e-302">Application administrator</span></span> | <span data-ttu-id="e451e-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="e451e-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="e451e-304">Sviluppatore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="e451e-304">Application developer</span></span> | <span data-ttu-id="e451e-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="e451e-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="e451e-306">Amministratore dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="e451e-306">Authentication administrator</span></span> | <span data-ttu-id="e451e-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="e451e-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="e451e-308">Amministratore di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="e451e-308">Azure DevOps administrator</span></span> | <span data-ttu-id="e451e-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="e451e-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="e451e-310">Amministratore di Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="e451e-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="e451e-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="e451e-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="e451e-312">Amministratore keyset Framework dell'esperienza B2C</span><span class="sxs-lookup"><span data-stu-id="e451e-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="e451e-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="e451e-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="e451e-314">Amministratore criteri B2C Framework dell'esperienza</span><span class="sxs-lookup"><span data-stu-id="e451e-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="e451e-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="e451e-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="e451e-316">Amministratore flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="e451e-316">B2C user flow administrator</span></span> | <span data-ttu-id="e451e-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="e451e-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="e451e-318">Amministratore attributi flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="e451e-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="e451e-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="e451e-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="e451e-320">Amministratore fatturazione</span><span class="sxs-lookup"><span data-stu-id="e451e-320">Billing administrator</span></span> | <span data-ttu-id="e451e-321">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="e451e-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="e451e-322">Amministratore di applicazioni cloud</span><span class="sxs-lookup"><span data-stu-id="e451e-322">Cloud application administrator</span></span> | <span data-ttu-id="e451e-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="e451e-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="e451e-324">Amministratore dispositivo cloud</span><span class="sxs-lookup"><span data-stu-id="e451e-324">Cloud device administrator</span></span> | <span data-ttu-id="e451e-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="e451e-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="e451e-326">Amministratore di conformità</span><span class="sxs-lookup"><span data-stu-id="e451e-326">Compliance administrator</span></span> | <span data-ttu-id="e451e-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="e451e-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="e451e-328">Amministratore dei dati sulla conformità</span><span class="sxs-lookup"><span data-stu-id="e451e-328">Compliance data administrator</span></span> | <span data-ttu-id="e451e-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="e451e-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="e451e-330">Amministratore di accesso condizionale</span><span class="sxs-lookup"><span data-stu-id="e451e-330">Conditional Access administrator</span></span> | <span data-ttu-id="e451e-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="e451e-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="e451e-332">Responsabile approvazione per l'accesso a Customer Lockbox</span><span class="sxs-lookup"><span data-stu-id="e451e-332">Customer LockBox access approver</span></span> | <span data-ttu-id="e451e-333">c18d54a8-B13E-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="e451e-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="e451e-334">Amministratore di analisi desktop</span><span class="sxs-lookup"><span data-stu-id="e451e-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="e451e-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="e451e-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="e451e-336">Ruoli con autorizzazioni di lettura nella directory</span><span class="sxs-lookup"><span data-stu-id="e451e-336">Directory readers</span></span> | <span data-ttu-id="e451e-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="e451e-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="e451e-338">Amministratore di Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="e451e-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="e451e-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="e451e-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="e451e-340">Amministratori di Exchange</span><span class="sxs-lookup"><span data-stu-id="e451e-340">Exchange administrator</span></span> | <span data-ttu-id="e451e-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="e451e-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="e451e-342">IdentityAmministratore provider esterno</span><span class="sxs-lookup"><span data-stu-id="e451e-342">External Identity Provider administrator</span></span> | <span data-ttu-id="e451e-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="e451e-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="e451e-344">Amministratore globale</span><span class="sxs-lookup"><span data-stu-id="e451e-344">Global administrator</span></span> | <span data-ttu-id="e451e-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="e451e-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="e451e-346">Ruolo con autorizzazioni di lettura globali</span><span class="sxs-lookup"><span data-stu-id="e451e-346">Global reader</span></span> | <span data-ttu-id="e451e-347">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="e451e-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="e451e-348">Amministratore di gruppi</span><span class="sxs-lookup"><span data-stu-id="e451e-348">Groups administrator</span></span> | <span data-ttu-id="e451e-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="e451e-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="e451e-350">Mittente dell'invito guest</span><span class="sxs-lookup"><span data-stu-id="e451e-350">Guest inviter</span></span> | <span data-ttu-id="e451e-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="e451e-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="e451e-352">Amministratore dell'help desk</span><span class="sxs-lookup"><span data-stu-id="e451e-352">Helpdesk administrator</span></span> | <span data-ttu-id="e451e-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="e451e-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="e451e-354">Amministratore di Intune</span><span class="sxs-lookup"><span data-stu-id="e451e-354">Intune administrator</span></span> | <span data-ttu-id="e451e-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="e451e-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="e451e-356">Amministratore di Kaizala</span><span class="sxs-lookup"><span data-stu-id="e451e-356">Kaizala administrator</span></span> | <span data-ttu-id="e451e-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="e451e-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="e451e-358">Amministratore licenze</span><span class="sxs-lookup"><span data-stu-id="e451e-358">License administrator</span></span> | <span data-ttu-id="e451e-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="e451e-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="e451e-360">Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="e451e-360">Message center privacy reader</span></span> | <span data-ttu-id="e451e-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="e451e-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="e451e-362">Ruolo con autorizzazioni di lettura per il Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="e451e-362">Message center reader</span></span> | <span data-ttu-id="e451e-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="e451e-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="e451e-364">Amministratore delle app di Office</span><span class="sxs-lookup"><span data-stu-id="e451e-364">Office apps administrator</span></span> | <span data-ttu-id="e451e-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="e451e-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="e451e-366">Amministratore password</span><span class="sxs-lookup"><span data-stu-id="e451e-366">Password administrator</span></span> | <span data-ttu-id="e451e-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="e451e-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="e451e-368">Amministratore di Power BI</span><span class="sxs-lookup"><span data-stu-id="e451e-368">Power BI administrator</span></span> | <span data-ttu-id="e451e-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="e451e-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="e451e-370">Amministratore di Power Platform</span><span class="sxs-lookup"><span data-stu-id="e451e-370">Power platform administrator</span></span> | <span data-ttu-id="e451e-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="e451e-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="e451e-372">Amministratore autenticazione con privilegi</span><span class="sxs-lookup"><span data-stu-id="e451e-372">Privileged authentication administrator</span></span> | <span data-ttu-id="e451e-373">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="e451e-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="e451e-374">Amministratore dei ruoli con privilegi</span><span class="sxs-lookup"><span data-stu-id="e451e-374">Privileged role administrator</span></span> | <span data-ttu-id="e451e-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="e451e-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="e451e-376">Lettore di report</span><span class="sxs-lookup"><span data-stu-id="e451e-376">Reports reader</span></span> | <span data-ttu-id="e451e-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="e451e-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="e451e-378">Amministratore della ricerca</span><span class="sxs-lookup"><span data-stu-id="e451e-378">Search administrator</span></span> | <span data-ttu-id="e451e-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="e451e-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="e451e-380">Editor della ricerca</span><span class="sxs-lookup"><span data-stu-id="e451e-380">Search editor</span></span> | <span data-ttu-id="e451e-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="e451e-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="e451e-382">Amministratore della sicurezza</span><span class="sxs-lookup"><span data-stu-id="e451e-382">Security administrator</span></span> | <span data-ttu-id="e451e-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="e451e-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="e451e-384">Operatore per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="e451e-384">Security operator</span></span> | <span data-ttu-id="e451e-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="e451e-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="e451e-386">Ruolo con autorizzazioni di lettura per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="e451e-386">Security reader</span></span> | <span data-ttu-id="e451e-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="e451e-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="e451e-388">Amministratore del supporto per il servizio</span><span class="sxs-lookup"><span data-stu-id="e451e-388">Service support administrator</span></span> | <span data-ttu-id="e451e-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="e451e-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="e451e-390">Amministratore di SharePoint</span><span class="sxs-lookup"><span data-stu-id="e451e-390">SharePoint administrator</span></span> | <span data-ttu-id="e451e-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="e451e-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="e451e-392">Amministratore di Skype for Business</span><span class="sxs-lookup"><span data-stu-id="e451e-392">Skype for Business administrator</span></span> | <span data-ttu-id="e451e-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="e451e-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="e451e-394">Amministratore comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="e451e-394">Teams Communications Administrator</span></span> | <span data-ttu-id="e451e-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="e451e-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="e451e-396">Tecnico di supporto comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="e451e-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="e451e-397">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="e451e-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="e451e-398">Specialista comunicazione Teams</span><span class="sxs-lookup"><span data-stu-id="e451e-398">Teams Communications Specialist</span></span> | <span data-ttu-id="e451e-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="e451e-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="e451e-400">Amministratore del servizio Teams</span><span class="sxs-lookup"><span data-stu-id="e451e-400">Teams Service Administrator</span></span> | <span data-ttu-id="e451e-401">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="e451e-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="e451e-402">Amministratore utenti</span><span class="sxs-lookup"><span data-stu-id="e451e-402">User administrator</span></span> | <span data-ttu-id="e451e-403">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="e451e-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e451e-404">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="e451e-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
