---
title: 'ASP.NET Core :::no-loc(Blazor WebAssembly)::: con gruppi e ruoli di Azure Active Directory'
author: guardrex
description: "Informazioni su come configurare :::no-loc(Blazor WebAssembly)::: per l'uso di gruppi e ruoli di Azure Active Directory."
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 7a56f03f2c3acd08b009673ef7533186bf703604
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690471"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="200ff-103">Gruppi di Azure Active Directory (AAD), ruoli di amministratore e ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="200ff-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="200ff-104">Di [Luke Latham](https://github.com/guardrex) e [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="200ff-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="200ff-105">Azure Active Directory (AAD) fornisce diversi approcci di autorizzazione che possono essere combinati con :::no-loc(ASP.NET Core Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="200ff-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="200ff-106">Gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="200ff-106">User-defined groups</span></span>
  * <span data-ttu-id="200ff-107">Sicurezza</span><span class="sxs-lookup"><span data-stu-id="200ff-107">Security</span></span>
  * <span data-ttu-id="200ff-108">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="200ff-108">Microsoft 365</span></span>
  * <span data-ttu-id="200ff-109">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="200ff-109">Distribution</span></span>
* <span data-ttu-id="200ff-110">Ruoli</span><span class="sxs-lookup"><span data-stu-id="200ff-110">Roles</span></span>
  * <span data-ttu-id="200ff-111">Ruoli di amministratore di AAD</span><span class="sxs-lookup"><span data-stu-id="200ff-111">AAD Administrator Roles</span></span>
  * <span data-ttu-id="200ff-112">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="200ff-112">User-defined roles</span></span>

<span data-ttu-id="200ff-113">Le indicazioni fornite in questo articolo sono valide per gli :::no-loc(Blazor WebAssembly)::: scenari di distribuzione di AAD descritti negli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="200ff-113">The guidance in this article applies to the :::no-loc(Blazor WebAssembly)::: AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="200ff-114">App autonoma con account Microsoft</span><span class="sxs-lookup"><span data-stu-id="200ff-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="200ff-115">App autonoma con AAD</span><span class="sxs-lookup"><span data-stu-id="200ff-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="200ff-116">App ospitata con AAD</span><span class="sxs-lookup"><span data-stu-id="200ff-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="200ff-117">Ambiti</span><span class="sxs-lookup"><span data-stu-id="200ff-117">Scopes</span></span>

<span data-ttu-id="200ff-118">È necessaria una chiamata [API Microsoft Graph](/graph/use-the-api) per qualsiasi utente dell'app con più di cinque appartenenze ai gruppi di sicurezza e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="200ff-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="200ff-119">Per consentire le chiamate di API Graph, assegnare all'app autonoma o *`Client`* di una soluzione ospitata una :::no-loc(Blazor)::: delle seguenti [autorizzazioni di API Graph (ambiti)](/graph/permissions-reference) nel portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="200ff-119">To permit Graph API calls, give the standalone or *`Client`* app of a hosted :::no-loc(Blazor)::: solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="200ff-120">`Directory.Read.All` è l'ambito con privilegi minimi e è l'ambito usato per l'esempio descritto in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="200ff-120">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="200ff-121">Gruppi definiti dall'utente e ruoli di amministratore</span><span class="sxs-lookup"><span data-stu-id="200ff-121">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="200ff-122">Per configurare l'app nella portale di Azure per fornire un' `groups` attestazione di appartenenza, vedere gli articoli seguenti su Azure.</span><span class="sxs-lookup"><span data-stu-id="200ff-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="200ff-123">Assegnare gli utenti ai gruppi AAD definiti dall'utente e ai ruoli di amministratore di AAD.</span><span class="sxs-lookup"><span data-stu-id="200ff-123">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="200ff-124">Definizione dei ruoli con i gruppi di sicurezza di Azure AD</span><span class="sxs-lookup"><span data-stu-id="200ff-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="200ff-125">`groupMembershipClaims` attributo</span><span class="sxs-lookup"><span data-stu-id="200ff-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="200ff-126">Gli esempi seguenti presuppongono che un utente sia assegnato al ruolo di *amministratore fatturazione* di AAD.</span><span class="sxs-lookup"><span data-stu-id="200ff-126">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="200ff-127">La singola `groups` attestazione inviata da AAD presenta i gruppi e i ruoli dell'utente come ID oggetto (Guid) in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="200ff-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="200ff-128">L'app deve convertire la matrice JSON di gruppi e ruoli in singole `group` attestazioni in base a cui l'app può compilare i [criteri](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="200ff-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="200ff-129">Quando il numero di ruoli di amministratore e gruppi definiti dall'utente assegnati è superiore a cinque, AAD Invia un' `hasgroups` attestazione con un `true` valore anziché inviare un' `groups` attestazione.</span><span class="sxs-lookup"><span data-stu-id="200ff-129">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="200ff-130">Qualsiasi app che può avere più di cinque ruoli e gruppi assegnati ai propri utenti deve effettuare una chiamata separata API Graph per ottenere i ruoli e i gruppi di un utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="200ff-131">L'implementazione di esempio disponibile in questo articolo illustra questo scenario.</span><span class="sxs-lookup"><span data-stu-id="200ff-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="200ff-132">Per altre informazioni, vedere l' `groups` articolo relativo alle `hasgroups` informazioni sulle attestazioni e in [token di accesso della piattaforma Microsoft Identity: payload Claims](/azure/active-directory/develop/access-tokens#payload-claims) .</span><span class="sxs-lookup"><span data-stu-id="200ff-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="200ff-133">Estendi <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> per includere le proprietà della matrice per gruppi e ruoli.</span><span class="sxs-lookup"><span data-stu-id="200ff-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="200ff-134">Assegnare una matrice vuota a ogni proprietà in modo che il controllo `null` non sia necessario quando queste proprietà vengono usate nei cicli in un `foreach` secondo momento.</span><span class="sxs-lookup"><span data-stu-id="200ff-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="200ff-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="200ff-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="200ff-136">Usare **uno** degli approcci seguenti per creare attestazioni per i gruppi e i ruoli di AAD:</span><span class="sxs-lookup"><span data-stu-id="200ff-136">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="200ff-137">Usare Graph SDK</span><span class="sxs-lookup"><span data-stu-id="200ff-137">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="200ff-138">Usa un oggetto denominato `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="200ff-138">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="200ff-139">Usare Graph SDK</span><span class="sxs-lookup"><span data-stu-id="200ff-139">Use the Graph SDK</span></span>

<span data-ttu-id="200ff-140">Aggiungere un riferimento al pacchetto all'app autonoma o all' *`Client`* app di una soluzione ospitata :::no-loc(Blazor)::: per [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) .</span><span class="sxs-lookup"><span data-stu-id="200ff-140">Add a package reference to the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="200ff-141">Aggiungere le classi e la configurazione dell'utilità Graph SDK nella sezione *Graph SDK* dell' <xref:blazor/security/webassembly/graph-api#graph-sdk> articolo.</span><span class="sxs-lookup"><span data-stu-id="200ff-141">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="200ff-142">Aggiungere la factory di account utente personalizzata seguente alla APPO autonoma o all' *`Client`* app di una soluzione ospitata :::no-loc(Blazor)::: ( `CustomAccountFactory.cs` ).</span><span class="sxs-lookup"><span data-stu-id="200ff-142">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted :::no-loc(Blazor)::: solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="200ff-143">La Factory utente personalizzata viene usata per elaborare le attestazioni di ruoli e gruppi.</span><span class="sxs-lookup"><span data-stu-id="200ff-143">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="200ff-144">La `roles` matrice di attestazioni è analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="200ff-144">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="200ff-145">Se l' `hasgroups` attestazione è presente, Graph SDK viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente:</span><span class="sxs-lookup"><span data-stu-id="200ff-145">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = user:::no-loc(Identity):::.Claims.FirstOrDefault(x => x.Type == "oid")?
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
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                user:::no-loc(Identity):::.RemoveClaim(claim);
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="200ff-146">Il codice precedente non include le appartenenze transitive.</span><span class="sxs-lookup"><span data-stu-id="200ff-146">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="200ff-147">Se l'app richiede attestazioni di appartenenza a gruppi dirette e transitive:</span><span class="sxs-lookup"><span data-stu-id="200ff-147">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="200ff-148">Modificare il `IUserMemberOfCollectionWithReferencesPage` tipo per `groupsAndAzureRoles` in `IUserTransitiveMemberOfCollectionWithReferencesPage` .</span><span class="sxs-lookup"><span data-stu-id="200ff-148">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="200ff-149">Quando si richiedono i gruppi e i ruoli dell'utente, sostituire la `MemberOf` proprietà con `TransitiveMemberOf` .</span><span class="sxs-lookup"><span data-stu-id="200ff-149">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="200ff-150">In `Program.Main` ( `Program.cs` ) configurare l'autenticazione MSAL per l'uso della factory dell'account utente personalizzata: se l'app usa una classe di account utente personalizzata che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> , scambiare la classe di account utente personalizzata per <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="200ff-150">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="200ff-151">Usa un oggetto denominato `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="200ff-151">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="200ff-152">Nell'app autonoma o nell' *`Client`* app di una soluzione ospitata :::no-loc(Blazor)::: creare una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata.</span><span class="sxs-lookup"><span data-stu-id="200ff-152">In the standalone app or the *`Client`* app of a hosted :::no-loc(Blazor)::: solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="200ff-153">Utilizzare l'ambito corretto per API Graph le chiamate che ottengono informazioni sui ruoli e sui gruppi.</span><span class="sxs-lookup"><span data-stu-id="200ff-153">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="200ff-154">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="200ff-154">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="200ff-155">In `Program.Main` ( `Program.cs` ) aggiungere il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio di implementazione e aggiungere un oggetto denominato <xref:System.Net.Http.HttpClient> per eseguire richieste di API Graph.</span><span class="sxs-lookup"><span data-stu-id="200ff-155">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="200ff-156">Nell'esempio seguente viene denominato il client `GraphAPI` :</span><span class="sxs-lookup"><span data-stu-id="200ff-156">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="200ff-157">Creare classi di oggetti directory di AAD per ricevere i ruoli e i gruppi di Open Data Protocol (OData) da una chiamata di API Graph.</span><span class="sxs-lookup"><span data-stu-id="200ff-157">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="200ff-158">OData arriva in formato JSON e una chiamata a <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> popola un'istanza della `DirectoryObjects` classe.</span><span class="sxs-lookup"><span data-stu-id="200ff-158">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="200ff-159">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="200ff-159">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="200ff-160">Creare una factory utente personalizzata per elaborare le attestazioni dei ruoli e dei gruppi.</span><span class="sxs-lookup"><span data-stu-id="200ff-160">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="200ff-161">Nell'implementazione di esempio seguente viene anche gestita la `roles` matrice di attestazioni, che viene analizzata nella sezione [ruoli definiti dall'utente](#user-defined-roles) .</span><span class="sxs-lookup"><span data-stu-id="200ff-161">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="200ff-162">Se l' `hasgroups` attestazione è presente, il nome <xref:System.Net.Http.HttpClient> viene usato per effettuare una richiesta autorizzata a API Graph per ottenere i ruoli e i gruppi dell'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-162">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="200ff-163">Questa implementazione usa l'endpoint di Microsoft :::no-loc(Identity)::: Platform v 1.0 `https://graph.microsoft.com/v1.0/me/memberOf` ([documentazione dell'API](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="200ff-163">This implementation uses the Microsoft :::no-loc(Identity)::: Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="200ff-164">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="200ff-164">`CustomAccountFactory.cs`:</span></span>

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

        if (initialUser.:::no-loc(Identity):::.IsAuthenticated)
        {
            var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)initialUser.:::no-loc(Identity):::;

            foreach (var role in account.Roles)
            {
                user:::no-loc(Identity):::.AddClaim(new Claim("role", role));
            }

            if (user:::no-loc(Identity):::.HasClaim(c => c.Type == "hasgroups"))
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
                            user:::no-loc(Identity):::.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = user:::no-loc(Identity):::.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        user:::no-loc(Identity):::.RemoveClaim(claim);
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
                    user:::no-loc(Identity):::.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="200ff-165">Non è necessario fornire codice per rimuovere l' `groups` attestazione originale, se presente, perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="200ff-165">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="200ff-166">L'approccio in questo esempio:</span><span class="sxs-lookup"><span data-stu-id="200ff-166">The approach in this example:</span></span>
>
> * <span data-ttu-id="200ff-167">Aggiunge una <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> classe personalizzata per alleghire i token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="200ff-167">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="200ff-168">Aggiunge un oggetto denominato <xref:System.Net.Http.HttpClient> per l'esecuzione di richieste API Web a un endpoint sicuro dell'API Web esterno.</span><span class="sxs-lookup"><span data-stu-id="200ff-168">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="200ff-169">Usa il denominato <xref:System.Net.Http.HttpClient> per eseguire richieste autorizzate.</span><span class="sxs-lookup"><span data-stu-id="200ff-169">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="200ff-170">Il code coverage generale per questo approccio è disponibile in questo <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> articolo.</span><span class="sxs-lookup"><span data-stu-id="200ff-170">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="200ff-171">Registrare la factory in `Program.Main` ( `Program.cs` ) dell'app autonoma o *`Client`* dell'app di una :::no-loc(Blazor)::: soluzione ospitata.</span><span class="sxs-lookup"><span data-stu-id="200ff-171">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution.</span></span> <span data-ttu-id="200ff-172">Consenso all' `Directory.Read.All` ambito come ambito aggiuntivo per l'app:</span><span class="sxs-lookup"><span data-stu-id="200ff-172">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="200ff-173">Configurazione dell'autorizzazione</span><span class="sxs-lookup"><span data-stu-id="200ff-173">Authorization configuration</span></span>

<span data-ttu-id="200ff-174">Creare un [criterio](xref:security/authorization/policies) per ogni gruppo o ruolo in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="200ff-174">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="200ff-175">L'esempio seguente crea un criterio per il ruolo di *amministratore fatturazione* di AAD:</span><span class="sxs-lookup"><span data-stu-id="200ff-175">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="200ff-176">Per l'elenco completo degli ID oggetto ruolo AAD, vedere la sezione [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids) .</span><span class="sxs-lookup"><span data-stu-id="200ff-176">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="200ff-177">Negli esempi seguenti l'app usa il criterio precedente per autorizzare l'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-177">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="200ff-178">Il [ `AuthorizeView` componente](xref:blazor/security/index#authorizeview-component) funziona con i criteri:</span><span class="sxs-lookup"><span data-stu-id="200ff-178">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="200ff-179">L'accesso a un intero componente può essere basato sui criteri che usano la [ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ):</span><span class="sxs-lookup"><span data-stu-id="200ff-179">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="200ff-180">Se l'utente non è connesso, viene reindirizzato alla pagina di accesso di AAD e quindi di nuovo al componente dopo l'accesso.</span><span class="sxs-lookup"><span data-stu-id="200ff-180">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="200ff-181">Un controllo dei criteri può essere [eseguito anche nel codice con la logica procedurale](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="200ff-181">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="200ff-182">Autorizzare l'accesso all'API server per i ruoli di amministratore e i gruppi definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="200ff-182">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="200ff-183">Oltre ad autorizzare gli utenti nell'app webassembly sul lato client ad accedere a pagine e risorse, l'API server può autorizzare gli utenti ad accedere agli endpoint API protetti.</span><span class="sxs-lookup"><span data-stu-id="200ff-183">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="200ff-184">Dopo che l'app *Server* convalida il token di accesso dell'utente:</span><span class="sxs-lookup"><span data-stu-id="200ff-184">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="200ff-185">L'app per le API server usa l' [attestazione dell'identificatore di oggetto `oid` ](/azure/active-directory/develop/id-tokens#payload-claims) non modificabile dell'utente () dal relativo token di accesso per ottenere un token di accesso per API Graph.</span><span class="sxs-lookup"><span data-stu-id="200ff-185">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="200ff-186">Una chiamata API Graph ottiene le appartenenze ai ruoli di amministratore e del gruppo di sicurezza definiti dall'utente di Azure dell'utente chiamando [`memberOf`](/graph/api/user-list-memberof) sull'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-186">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="200ff-187">Le appartenenze vengono usate per stabilire le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="200ff-187">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="200ff-188">È possibile usare i [criteri di autorizzazione](xref:security/authorization/policies) per limitare l'accesso degli utenti agli endpoint dell'API server nell'intera app.</span><span class="sxs-lookup"><span data-stu-id="200ff-188">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="200ff-189">Questa guida attualmente non include l'autorizzazione degli utenti sulla base dei [ruoli definiti dall'utente di AAD](#user-defined-roles).</span><span class="sxs-lookup"><span data-stu-id="200ff-189">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="200ff-190">Le indicazioni fornite in questa sezione configurano l'app per le API server come [*app daemon*](/azure/active-directory/develop/scenario-daemon-overview) per la chiamata API Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="200ff-190">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="200ff-191">Questo approccio **non** :</span><span class="sxs-lookup"><span data-stu-id="200ff-191">This approach does **not** :</span></span>

* <span data-ttu-id="200ff-192">Richiedere l' `access_as_user` ambito.</span><span class="sxs-lookup"><span data-stu-id="200ff-192">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="200ff-193">Accedere API Graph per conto dell'utente o del client che effettua la richiesta dell'API.</span><span class="sxs-lookup"><span data-stu-id="200ff-193">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="200ff-194">La chiamata a API Graph dall'app per le API server richiede solo un' **applicazione** per l'app per le api Server API Graph ambito per `Directory.Read.All` nel portale di Azure.</span><span class="sxs-lookup"><span data-stu-id="200ff-194">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="200ff-195">Questo approccio impedisce assolutamente all'app client di accedere ai dati della directory non consentiti in modo esplicito dall'API del server.</span><span class="sxs-lookup"><span data-stu-id="200ff-195">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="200ff-196">L'app client può accedere solo agli endpoint controller dell'app per le API del server.</span><span class="sxs-lookup"><span data-stu-id="200ff-196">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="200ff-197">Configurazione di Azure</span><span class="sxs-lookup"><span data-stu-id="200ff-197">Azure configuration</span></span>

* <span data-ttu-id="200ff-198">Verificare che alla registrazione dell'app *Server* venga assegnata l' **applicazione** (non **delegata** ) API Graph ambito per `Directory.Read.All` , che rappresenta il livello di accesso con privilegi minimi per i gruppi di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="200ff-198">Confirm that the *Server* app registration is given **Application** (not **Delegated** ) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="200ff-199">Verificare che l'autorizzazione dell'amministratore venga applicata all'ambito dopo aver eseguito l'assegnazione dell'ambito.</span><span class="sxs-lookup"><span data-stu-id="200ff-199">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="200ff-200">Assegnare un nuovo segreto client all'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="200ff-200">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="200ff-201">Prendere nota del segreto per la configurazione dell'app nella sezione [impostazioni app](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="200ff-201">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="200ff-202">Impostazioni delle app</span><span class="sxs-lookup"><span data-stu-id="200ff-202">App settings</span></span>

<span data-ttu-id="200ff-203">Nel file di impostazioni dell'app ( `appsettings.json` o `appsettings.Production.json` ) creare una `ClientSecret` voce con il segreto client dell'app *Server* dalla portale di Azure:</span><span class="sxs-lookup"><span data-stu-id="200ff-203">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="200ff-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="200ff-204">For example:</span></span>

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
> <span data-ttu-id="200ff-205">Se il dominio dell'editore tenant non viene verificato, l'ambito dell'API server per l'accesso utente/client usa un `https://` URI basato su.</span><span class="sxs-lookup"><span data-stu-id="200ff-205">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="200ff-206">In questo scenario l'app per le API server richiede la `Audience` configurazione nel `appsettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="200ff-206">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="200ff-207">Nella configurazione seguente, la fine del valore non `Audience` include l' **not** ambito predefinito `/{DEFAULT SCOPE}` , in cui il segnaposto `{DEFAULT SCOPE}` è l'ambito predefinito:</span><span class="sxs-lookup"><span data-stu-id="200ff-207">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
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
> <span data-ttu-id="200ff-208">Nella configurazione dell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="200ff-208">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="200ff-209">Il dominio del tenant è `contoso.onmicrosoft.com` .</span><span class="sxs-lookup"><span data-stu-id="200ff-209">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="200ff-210">L'app per le API del server `ClientId` è `41451fa7-82d9-4673-8fa5-69eff5a761fd` .</span><span class="sxs-lookup"><span data-stu-id="200ff-210">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="200ff-211">`Audience` **Non** è in genere necessario configurare un oggetto in modo esplicito per le app con un dominio Publisher verificato con `api://` ambito API basato su.</span><span class="sxs-lookup"><span data-stu-id="200ff-211">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="200ff-212">Per altre informazioni, vedere <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="200ff-212">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="200ff-213">I Criteri di autorizzazione</span><span class="sxs-lookup"><span data-stu-id="200ff-213">Authorization policies</span></span>

<span data-ttu-id="200ff-214">Creare [criteri di autorizzazione](xref:security/authorization/policies) per i gruppi di sicurezza AAD e i ruoli di amministratore di AAD nell'app *Server* `Startup.ConfigureServices` ( `Startup.cs` ) basata sugli ID oggetto gruppo e gli [ID oggetto ruolo amministratore di AAD](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="200ff-214">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="200ff-215">Un criterio del ruolo amministratore fatturazione di Azure, ad esempio, ha la configurazione seguente:</span><span class="sxs-lookup"><span data-stu-id="200ff-215">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="200ff-216">Per altre informazioni, vedere <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="200ff-216">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="200ff-217">Accesso al controller</span><span class="sxs-lookup"><span data-stu-id="200ff-217">Controller access</span></span>

<span data-ttu-id="200ff-218">Richiedere criteri nei controller dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="200ff-218">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="200ff-219">Nell'esempio seguente viene limitato l'accesso ai dati di fatturazione da `BillingDataController` ad amministratori della fatturazione di Azure con un nome di criterio `BillingAdmin` , come configurato nella sezione [criteri di autorizzazione](#authorization-policies) :</span><span class="sxs-lookup"><span data-stu-id="200ff-219">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

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

### <a name="packages"></a><span data-ttu-id="200ff-220">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="200ff-220">Packages</span></span>

<span data-ttu-id="200ff-221">Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="200ff-221">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="200ff-222">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="200ff-222">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="200ff-223">[Microsoft. :::no-loc(Identity)::: . Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span><span class="sxs-lookup"><span data-stu-id="200ff-223">[Microsoft.:::no-loc(Identity):::.Client](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Client)</span></span>

### <a name="services"></a><span data-ttu-id="200ff-224">Servizi</span><span class="sxs-lookup"><span data-stu-id="200ff-224">Services</span></span>

<span data-ttu-id="200ff-225">Nel metodo *Server* dell'app Server `Startup.ConfigureServices` sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="200ff-225">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="200ff-226">Aggiungere gli spazi dei nomi seguenti a `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="200ff-226">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.:::no-loc(Identity):::.Client;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="200ff-227">Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="200ff-227">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="200ff-228">Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="200ff-228">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="200ff-229">Ad esempio, l'app può registrare l'autenticazione non riuscita.</span><span class="sxs-lookup"><span data-stu-id="200ff-229">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="200ff-230">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-230">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="200ff-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="200ff-231"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="200ff-232">Attiva solo le informazioni personali per il debug con gli account utente di test.</span><span class="sxs-lookup"><span data-stu-id="200ff-232">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="200ff-233">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="200ff-233">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(options =>
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
                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

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
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
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

<span data-ttu-id="200ff-234">Nel codice precedente, la gestione degli errori di token seguenti è facoltativa:</span><span class="sxs-lookup"><span data-stu-id="200ff-234">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="200ff-235">`MsalUiRequiredException`: L'app non ha autorizzazioni sufficienti (ambiti).</span><span class="sxs-lookup"><span data-stu-id="200ff-235">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="200ff-236">Determinare se gli ambiti delle app per le API del server nel portale di Azure includono un'autorizzazione per l' **applicazione** `Directory.Read.All` .</span><span class="sxs-lookup"><span data-stu-id="200ff-236">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="200ff-237">Verificare che l'amministratore tenant abbia concesso le autorizzazioni per l'app.</span><span class="sxs-lookup"><span data-stu-id="200ff-237">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="200ff-238">`MsalServiceException` ( `AADSTS70011` ): Verificare che l'ambito sia `https://graph.microsoft.com/.default` .</span><span class="sxs-lookup"><span data-stu-id="200ff-238">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="200ff-239">Pacchetti</span><span class="sxs-lookup"><span data-stu-id="200ff-239">Packages</span></span>

<span data-ttu-id="200ff-240">Aggiungere i riferimenti al pacchetto all'app *Server* per i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="200ff-240">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="200ff-241">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="200ff-241">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="200ff-242">[Microsoft. :::no-loc(Identity)::: Model. clients. ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="200ff-242">[Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="200ff-243">Configurazione del servizio</span><span class="sxs-lookup"><span data-stu-id="200ff-243">Service configuration</span></span>

<span data-ttu-id="200ff-244">Nel metodo *Server* dell'app Server `Startup.ConfigureServices` aggiungere la logica per eseguire la chiamata API Graph e stabilire le `group` attestazioni utente per i gruppi di sicurezza e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-244">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="200ff-245">Il codice di esempio in questa sezione usa il Active Directory Authentication Library (ADAL), che è basato su Microsoft :::no-loc(Identity)::: Platform v 1.0.</span><span class="sxs-lookup"><span data-stu-id="200ff-245">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft :::no-loc(Identity)::: Platform v1.0.</span></span>

<span data-ttu-id="200ff-246">Sono necessari altri spazi dei nomi per il codice nella `Startup` classe dell'app *Server* .</span><span class="sxs-lookup"><span data-stu-id="200ff-246">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="200ff-247">Il set di `using` istruzioni seguente include gli spazi dei nomi richiesti per il codice seguente in questa sezione:</span><span class="sxs-lookup"><span data-stu-id="200ff-247">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

```csharp
using System;
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
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
using Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory;
using Microsoft.:::no-loc(Identity):::Model.Logging;
```

<span data-ttu-id="200ff-248">Quando si configura <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents> :</span><span class="sxs-lookup"><span data-stu-id="200ff-248">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="200ff-249">Includere facoltativamente l'elaborazione per <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="200ff-249">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="200ff-250">Ad esempio, l'app può registrare l'autenticazione non riuscita.</span><span class="sxs-lookup"><span data-stu-id="200ff-250">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="200ff-251">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> , effettuare una chiamata API Graph per ottenere i gruppi e i ruoli dell'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-251">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="200ff-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> fornisce informazioni personali per la registrazione dei messaggi.</span><span class="sxs-lookup"><span data-stu-id="200ff-252"><xref:Microsoft.:::no-loc(Identity):::Model.Logging.:::no-loc(Identity):::ModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="200ff-253">Attiva solo le informazioni personali per il debug con gli account utente di test.</span><span class="sxs-lookup"><span data-stu-id="200ff-253">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="200ff-254">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="200ff-254">In `Startup.ConfigureServices`:</span></span>

```csharp
#if DEBUG
:::no-loc(Identity):::ModelEventSource.ShowPII = true;
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

                var user:::no-loc(Identity)::: = (Claims:::no-loc(Identity):::)context.Principal.:::no-loc(Identity):::;

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
                        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="200ff-255">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="200ff-255">In the preceding example:</span></span>

* <span data-ttu-id="200ff-256"><xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>Viene effettuato un tentativo di acquisizione del token invisibile all'utente () perché il token di accesso potrebbe essere già stato archiviato nella cache dei token adal.</span><span class="sxs-lookup"><span data-stu-id="200ff-256">Silent token acquisition (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="200ff-257">È più veloce ottenere il token dalla cache anziché richiedere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="200ff-257">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="200ff-258">Se il token di accesso non viene acquisito dalla cache ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> o <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> viene generata), viene eseguita un'asserzione utente ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion> ) con la credenziale client ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential> ) per ottenere il token per conto dell'utente ( <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A> ).</span><span class="sxs-lookup"><span data-stu-id="200ff-258">If the access token isn't acquired from cache (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="200ff-259">Successivamente, il `Microsoft.Graph.GraphServiceClient` può continuare a usare il token per effettuare la chiamata API Graph.</span><span class="sxs-lookup"><span data-stu-id="200ff-259">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="200ff-260">Il token viene inserito nella cache dei token ADAL.</span><span class="sxs-lookup"><span data-stu-id="200ff-260">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="200ff-261">Per API Graph future chiamate per lo stesso utente, il token viene acquisito dalla cache in modo invisibile all'utente con <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="200ff-261">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.:::no-loc(Identity):::Model.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="200ff-262">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non ottiene le appartenenze transitive.</span><span class="sxs-lookup"><span data-stu-id="200ff-262">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="200ff-263">Per modificare il codice per ottenere l'appartenenza diretta e transitiva:</span><span class="sxs-lookup"><span data-stu-id="200ff-263">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="200ff-264">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="200ff-264">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="200ff-265">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="200ff-265">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="200ff-266">Per la riga di codice:</span><span class="sxs-lookup"><span data-stu-id="200ff-266">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="200ff-267">Sostituire la riga precedente con:</span><span class="sxs-lookup"><span data-stu-id="200ff-267">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="200ff-268">Il codice in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> non distingue tra i gruppi di sicurezza AAD e i ruoli di amministratore di AAD durante la creazione di attestazioni.</span><span class="sxs-lookup"><span data-stu-id="200ff-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="200ff-269">Per fare in modo che l'app distingua tra gruppi e ruoli, controllare `entry.ODataType` quando si scorrono i gruppi e i ruoli.</span><span class="sxs-lookup"><span data-stu-id="200ff-269">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="200ff-270">Per creare attestazioni di ruolo e gruppi di sicurezza separati, usare codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="200ff-270">To create separate security group and role claims, use code similar to the following:</span></span>

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        user:::no-loc(Identity):::.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        user:::no-loc(Identity):::.AddClaim(new Claim("role", entry.Id));
    }
}
```

## <a name="user-defined-roles"></a><span data-ttu-id="200ff-271">Ruoli definiti dall'utente</span><span class="sxs-lookup"><span data-stu-id="200ff-271">User-defined roles</span></span>

<span data-ttu-id="200ff-272">È anche possibile configurare un'app registrata da AAD per usare i ruoli definiti dall'utente.</span><span class="sxs-lookup"><span data-stu-id="200ff-272">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="200ff-273">Per configurare l'app nella portale di Azure per fornire un' `roles` attestazione di appartenenza, vedere [procedura: aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="200ff-273">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="200ff-274">Nell'esempio seguente si presuppone che un'app sia configurata con due ruoli:</span><span class="sxs-lookup"><span data-stu-id="200ff-274">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="200ff-275">Sebbene non sia possibile assegnare ruoli a gruppi di sicurezza senza un account Azure AD Premium, è possibile assegnare utenti ai ruoli e ricevere un' `roles` attestazione per gli utenti con un account Azure standard.</span><span class="sxs-lookup"><span data-stu-id="200ff-275">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="200ff-276">Le indicazioni fornite in questa sezione non richiedono un account Azure AD Premium.</span><span class="sxs-lookup"><span data-stu-id="200ff-276">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="200ff-277">Nel portale di Azure vengono assegnati più ruoli aggiungendo **_nuovamente un utente_** per ogni assegnazione di ruolo aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="200ff-277">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="200ff-278">La singola `roles` attestazione inviata da AAD presenta i ruoli definiti dall'utente come `appRoles` `value` s in una matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="200ff-278">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="200ff-279">L'app deve convertire la matrice JSON dei ruoli in singole `role` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="200ff-279">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="200ff-280">Il `CustomUserFactory` illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) è configurato per agire su un' `roles` attestazione con un valore di matrice JSON.</span><span class="sxs-lookup"><span data-stu-id="200ff-280">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="200ff-281">Aggiungere e registrare `CustomUserFactory` nell'app autonoma o in *`Client`* un'app di una :::no-loc(Blazor)::: soluzione ospitata, come illustrato nella sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="200ff-281">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="200ff-282">Non è necessario fornire codice per rimuovere l' `roles` attestazione originale perché viene automaticamente rimossa dal Framework.</span><span class="sxs-lookup"><span data-stu-id="200ff-282">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="200ff-283">Nell'app `Program.Main` autonoma o *`Client`* app di una soluzione ospitata :::no-loc(Blazor)::: specificare l'attestazione denominata " `role` " come attestazione del ruolo:</span><span class="sxs-lookup"><span data-stu-id="200ff-283">In `Program.Main` of the standalone app or *`Client`* app of a hosted :::no-loc(Blazor)::: solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="200ff-284">A questo punto sono funzionali gli approcci di autorizzazione per i componenti.</span><span class="sxs-lookup"><span data-stu-id="200ff-284">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="200ff-285">Qualsiasi meccanismo di autorizzazione nei componenti può utilizzare il `admin` ruolo per autorizzare l'utente:</span><span class="sxs-lookup"><span data-stu-id="200ff-285">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="200ff-286">[ `AuthorizeView` Component](xref:blazor/security/index#authorizeview-component) (esempio: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="200ff-286">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="200ff-287">[ `[Authorize]` direttiva attribute](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (esempio: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="200ff-287">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="200ff-288">[Logica procedurale](xref:blazor/security/index#procedural-logic) (esempio: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="200ff-288">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="200ff-289">Sono supportati più test di ruolo:</span><span class="sxs-lookup"><span data-stu-id="200ff-289">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="200ff-290">ID oggetto ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="200ff-290">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="200ff-291">Gli ID oggetto presentati nella tabella seguente vengono usati per creare [criteri](xref:security/authorization/policies) per le `group` attestazioni.</span><span class="sxs-lookup"><span data-stu-id="200ff-291">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="200ff-292">I criteri consentono a un'app di autorizzare gli utenti per varie attività in un'app.</span><span class="sxs-lookup"><span data-stu-id="200ff-292">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="200ff-293">Per altre informazioni, vedere la sezione [gruppi definiti dall'utente e ruoli di amministratore di AAD](#user-defined-groups-and-administrator-roles) .</span><span class="sxs-lookup"><span data-stu-id="200ff-293">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="200ff-294">Ruolo amministratore AAD</span><span class="sxs-lookup"><span data-stu-id="200ff-294">AAD Administrator Role</span></span> | <span data-ttu-id="200ff-295">ID dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="200ff-295">Object ID</span></span>
--- | ---
<span data-ttu-id="200ff-296">Amministratore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="200ff-296">Application administrator</span></span> | <span data-ttu-id="200ff-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="200ff-297">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="200ff-298">Sviluppatore di applicazioni</span><span class="sxs-lookup"><span data-stu-id="200ff-298">Application developer</span></span> | <span data-ttu-id="200ff-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="200ff-299">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="200ff-300">Amministratore dell'autenticazione</span><span class="sxs-lookup"><span data-stu-id="200ff-300">Authentication administrator</span></span> | <span data-ttu-id="200ff-301">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="200ff-301">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="200ff-302">Amministratore di Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="200ff-302">Azure DevOps administrator</span></span> | <span data-ttu-id="200ff-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="200ff-303">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="200ff-304">Amministratore di Azure Information Protection</span><span class="sxs-lookup"><span data-stu-id="200ff-304">Azure Information Protection administrator</span></span> | <span data-ttu-id="200ff-305">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="200ff-305">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="200ff-306">Amministratore keyset Framework dell'esperienza B2C</span><span class="sxs-lookup"><span data-stu-id="200ff-306">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="200ff-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="200ff-307">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="200ff-308">Amministratore criteri B2C Framework dell'esperienza</span><span class="sxs-lookup"><span data-stu-id="200ff-308">B2C IEF Policy administrator</span></span> | <span data-ttu-id="200ff-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="200ff-309">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="200ff-310">Amministratore flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="200ff-310">B2C user flow administrator</span></span> | <span data-ttu-id="200ff-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="200ff-311">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="200ff-312">Amministratore attributi flusso utente B2C</span><span class="sxs-lookup"><span data-stu-id="200ff-312">B2C user flow attribute administrator</span></span> | <span data-ttu-id="200ff-313">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="200ff-313">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="200ff-314">Amministratore fatturazione</span><span class="sxs-lookup"><span data-stu-id="200ff-314">Billing administrator</span></span> | <span data-ttu-id="200ff-315">69ff516a-B57D-4697-A429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="200ff-315">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="200ff-316">Amministratore di applicazioni cloud</span><span class="sxs-lookup"><span data-stu-id="200ff-316">Cloud application administrator</span></span> | <span data-ttu-id="200ff-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="200ff-317">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="200ff-318">Amministratore dispositivo cloud</span><span class="sxs-lookup"><span data-stu-id="200ff-318">Cloud device administrator</span></span> | <span data-ttu-id="200ff-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="200ff-319">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="200ff-320">Amministratore di conformità</span><span class="sxs-lookup"><span data-stu-id="200ff-320">Compliance administrator</span></span> | <span data-ttu-id="200ff-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="200ff-321">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="200ff-322">Amministratore dei dati sulla conformità</span><span class="sxs-lookup"><span data-stu-id="200ff-322">Compliance data administrator</span></span> | <span data-ttu-id="200ff-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="200ff-323">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="200ff-324">Amministratore di accesso condizionale</span><span class="sxs-lookup"><span data-stu-id="200ff-324">Conditional Access administrator</span></span> | <span data-ttu-id="200ff-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="200ff-325">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="200ff-326">Responsabile approvazione per l'accesso a Customer Lockbox</span><span class="sxs-lookup"><span data-stu-id="200ff-326">Customer LockBox access approver</span></span> | <span data-ttu-id="200ff-327">c18d54a8-B13E-4954-A1A4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="200ff-327">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="200ff-328">Amministratore di analisi desktop</span><span class="sxs-lookup"><span data-stu-id="200ff-328">Desktop Analytics administrator</span></span> | <span data-ttu-id="200ff-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="200ff-329">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="200ff-330">Ruoli con autorizzazioni di lettura nella directory</span><span class="sxs-lookup"><span data-stu-id="200ff-330">Directory readers</span></span> | <span data-ttu-id="200ff-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="200ff-331">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="200ff-332">Amministratore di Dynamics 365</span><span class="sxs-lookup"><span data-stu-id="200ff-332">Dynamics 365 administrator</span></span> | <span data-ttu-id="200ff-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="200ff-333">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="200ff-334">Amministratori di Exchange</span><span class="sxs-lookup"><span data-stu-id="200ff-334">Exchange administrator</span></span> | <span data-ttu-id="200ff-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="200ff-335">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="200ff-336">:::no-loc(Identity):::Amministratore provider esterno</span><span class="sxs-lookup"><span data-stu-id="200ff-336">External :::no-loc(Identity)::: Provider administrator</span></span> | <span data-ttu-id="200ff-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="200ff-337">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="200ff-338">Amministratore globale</span><span class="sxs-lookup"><span data-stu-id="200ff-338">Global administrator</span></span> | <span data-ttu-id="200ff-339">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="200ff-339">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="200ff-340">Ruolo con autorizzazioni di lettura globali</span><span class="sxs-lookup"><span data-stu-id="200ff-340">Global reader</span></span> | <span data-ttu-id="200ff-341">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="200ff-341">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="200ff-342">Amministratore di gruppi</span><span class="sxs-lookup"><span data-stu-id="200ff-342">Groups administrator</span></span> | <span data-ttu-id="200ff-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="200ff-343">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="200ff-344">Mittente dell'invito guest</span><span class="sxs-lookup"><span data-stu-id="200ff-344">Guest inviter</span></span> | <span data-ttu-id="200ff-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="200ff-345">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="200ff-346">Amministratore dell'help desk</span><span class="sxs-lookup"><span data-stu-id="200ff-346">Helpdesk administrator</span></span> | <span data-ttu-id="200ff-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="200ff-347">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="200ff-348">Amministratore di Intune</span><span class="sxs-lookup"><span data-stu-id="200ff-348">Intune administrator</span></span> | <span data-ttu-id="200ff-349">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="200ff-349">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="200ff-350">Amministratore di Kaizala</span><span class="sxs-lookup"><span data-stu-id="200ff-350">Kaizala administrator</span></span> | <span data-ttu-id="200ff-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="200ff-351">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="200ff-352">Amministratore licenze</span><span class="sxs-lookup"><span data-stu-id="200ff-352">License administrator</span></span> | <span data-ttu-id="200ff-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="200ff-353">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="200ff-354">Ruolo con autorizzazioni di lettura per la privacy del Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="200ff-354">Message center privacy reader</span></span> | <span data-ttu-id="200ff-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="200ff-355">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="200ff-356">Ruolo con autorizzazioni di lettura per il Centro messaggi</span><span class="sxs-lookup"><span data-stu-id="200ff-356">Message center reader</span></span> | <span data-ttu-id="200ff-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="200ff-357">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="200ff-358">Amministratore delle app di Office</span><span class="sxs-lookup"><span data-stu-id="200ff-358">Office apps administrator</span></span> | <span data-ttu-id="200ff-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="200ff-359">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="200ff-360">Amministratore password</span><span class="sxs-lookup"><span data-stu-id="200ff-360">Password administrator</span></span> | <span data-ttu-id="200ff-361">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="200ff-361">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="200ff-362">Amministratore di Power BI</span><span class="sxs-lookup"><span data-stu-id="200ff-362">Power BI administrator</span></span> | <span data-ttu-id="200ff-363">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="200ff-363">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="200ff-364">Amministratore di Power Platform</span><span class="sxs-lookup"><span data-stu-id="200ff-364">Power platform administrator</span></span> | <span data-ttu-id="200ff-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="200ff-365">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="200ff-366">Amministratore autenticazione con privilegi</span><span class="sxs-lookup"><span data-stu-id="200ff-366">Privileged authentication administrator</span></span> | <span data-ttu-id="200ff-367">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="200ff-367">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="200ff-368">Amministratore dei ruoli con privilegi</span><span class="sxs-lookup"><span data-stu-id="200ff-368">Privileged role administrator</span></span> | <span data-ttu-id="200ff-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="200ff-369">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="200ff-370">Lettore di report</span><span class="sxs-lookup"><span data-stu-id="200ff-370">Reports reader</span></span> | <span data-ttu-id="200ff-371">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="200ff-371">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="200ff-372">Amministratore della ricerca</span><span class="sxs-lookup"><span data-stu-id="200ff-372">Search administrator</span></span> | <span data-ttu-id="200ff-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="200ff-373">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="200ff-374">Editor della ricerca</span><span class="sxs-lookup"><span data-stu-id="200ff-374">Search editor</span></span> | <span data-ttu-id="200ff-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="200ff-375">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="200ff-376">Amministratore della sicurezza</span><span class="sxs-lookup"><span data-stu-id="200ff-376">Security administrator</span></span> | <span data-ttu-id="200ff-377">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="200ff-377">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="200ff-378">Operatore per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="200ff-378">Security operator</span></span> | <span data-ttu-id="200ff-379">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="200ff-379">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="200ff-380">Ruolo con autorizzazioni di lettura per la sicurezza</span><span class="sxs-lookup"><span data-stu-id="200ff-380">Security reader</span></span> | <span data-ttu-id="200ff-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="200ff-381">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="200ff-382">Amministratore del supporto per il servizio</span><span class="sxs-lookup"><span data-stu-id="200ff-382">Service support administrator</span></span> | <span data-ttu-id="200ff-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="200ff-383">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="200ff-384">Amministratore di SharePoint</span><span class="sxs-lookup"><span data-stu-id="200ff-384">SharePoint administrator</span></span> | <span data-ttu-id="200ff-385">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="200ff-385">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="200ff-386">Amministratore di Skype for Business</span><span class="sxs-lookup"><span data-stu-id="200ff-386">Skype for Business administrator</span></span> | <span data-ttu-id="200ff-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="200ff-387">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="200ff-388">Amministratore comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="200ff-388">Teams Communications Administrator</span></span> | <span data-ttu-id="200ff-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="200ff-389">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="200ff-390">Tecnico di supporto comunicazioni Teams</span><span class="sxs-lookup"><span data-stu-id="200ff-390">Teams Communications Support Engineer</span></span> | <span data-ttu-id="200ff-391">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="200ff-391">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="200ff-392">Specialista comunicazione Teams</span><span class="sxs-lookup"><span data-stu-id="200ff-392">Teams Communications Specialist</span></span> | <span data-ttu-id="200ff-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="200ff-393">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="200ff-394">Amministratore del servizio Teams</span><span class="sxs-lookup"><span data-stu-id="200ff-394">Teams Service Administrator</span></span> | <span data-ttu-id="200ff-395">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="200ff-395">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="200ff-396">Amministratore utenti</span><span class="sxs-lookup"><span data-stu-id="200ff-396">User administrator</span></span> | <span data-ttu-id="200ff-397">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="200ff-397">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="200ff-398">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="200ff-398">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
