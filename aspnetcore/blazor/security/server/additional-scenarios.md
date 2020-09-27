---
title: ASP.NET Core Blazor Server scenari di sicurezza aggiuntivi
author: guardrex
description: Informazioni su come configurare Blazor Server per altri scenari di sicurezza.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393665"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="379ee-103">ASP.NET Core Blazor Server scenari di sicurezza aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="379ee-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="379ee-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="379ee-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="379ee-105">Passare i token a un' Blazor Server app</span><span class="sxs-lookup"><span data-stu-id="379ee-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="379ee-106">I token disponibili all'esterno dei Razor componenti di un' Blazor Server app possono essere passati ai componenti con l'approccio descritto in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="379ee-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="379ee-107">Per il codice di esempio, incluso un `Startup.ConfigureServices` esempio completo, vedere il [passaggio di token a un' Blazor applicazione lato server](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="379ee-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="379ee-108">Autenticare l' Blazor Server app come se si trattasse di un normale Razor sito o di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="379ee-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="379ee-109">Eseguire il provisioning e salvare i token nell'autenticazione cookie .</span><span class="sxs-lookup"><span data-stu-id="379ee-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="379ee-110">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="379ee-110">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="379ee-111">Definire una classe per passare lo stato iniziale dell'app con i token di accesso e di aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="379ee-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="379ee-112">Definire un servizio del provider di token con **ambito** che può essere usato nell' Blazor app per risolvere i token da [inserimento delle dipendenze](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="379ee-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="379ee-113">In `Startup.ConfigureServices` aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="379ee-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="379ee-114">Nel `_Host.cshtml` file creare un'istanza di e `InitialApplicationState` passarla come parametro all'app:</span><span class="sxs-lookup"><span data-stu-id="379ee-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="379ee-115">Nel `App` componente ( `App.razor` ) risolvere il servizio e inizializzarlo con i dati del parametro:</span><span class="sxs-lookup"><span data-stu-id="379ee-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="379ee-116">Aggiungere un riferimento al pacchetto all'app per il pacchetto NuGet [Microsoft. AspNet. WebAPI. client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) .</span><span class="sxs-lookup"><span data-stu-id="379ee-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="379ee-117">Nel servizio che esegue una richiesta API protetta, inserire il provider di token e recuperare il token per chiamare l'API:</span><span class="sxs-lookup"><span data-stu-id="379ee-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="379ee-118">Impostare lo schema di autenticazione</span><span class="sxs-lookup"><span data-stu-id="379ee-118">Set the authentication scheme</span></span>

<span data-ttu-id="379ee-119">Per un'app che usa più di un middleware di autenticazione e pertanto dispone di più di uno schema di autenticazione, lo schema Blazor usato da può essere impostato in modo esplicito nella configurazione dell'endpoint di `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="379ee-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="379ee-120">Nell'esempio seguente viene impostato lo schema di Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="379ee-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="379ee-121">Usare gli endpoint OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="379ee-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="379ee-122">I modelli e la libreria di autenticazione Blazor usano gli endpoint OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="379ee-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="379ee-123">Per usare un endpoint v 2.0, configurare l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opzione in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="379ee-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="379ee-124">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="379ee-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="379ee-125">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="379ee-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="379ee-126">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o nel file di impostazioni dell'app con la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> chiave.</span><span class="sxs-lookup"><span data-stu-id="379ee-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="379ee-127">Modifiche al codice</span><span class="sxs-lookup"><span data-stu-id="379ee-127">Code changes</span></span>

* <span data-ttu-id="379ee-128">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="379ee-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="379ee-129">Per ulteriori informazioni, vedere [perché eseguire l'aggiornamento alla piattaforma di identità Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="379ee-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="379ee-130">nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="379ee-130">in the Azure documentation.</span></span>
* <span data-ttu-id="379ee-131">Poiché le risorse vengono specificate negli URI di ambito per gli endpoint v 2.0, rimuovere l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> impostazione della proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="379ee-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="379ee-132">Per altre informazioni, vedere [ambiti, non risorse](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="379ee-132">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="379ee-133">URI ID app</span><span class="sxs-lookup"><span data-stu-id="379ee-133">App ID URI</span></span>

* <span data-ttu-id="379ee-134">Quando si usano gli endpoint v 2.0, le API definiscono un oggetto *`App ID URI`* , che è destinato a rappresentare un identificatore univoco per l'API.</span><span class="sxs-lookup"><span data-stu-id="379ee-134">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="379ee-135">Tutti gli ambiti includono l'URI ID app come prefisso, mentre gli endpoint v 2.0 emettono token di accesso con l'URI ID app come destinatari.</span><span class="sxs-lookup"><span data-stu-id="379ee-135">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="379ee-136">Quando si usano gli endpoint V 2.0, l'ID client configurato nell'API del server cambia dall'ID applicazione (ID client) dell'API all'URI ID app.</span><span class="sxs-lookup"><span data-stu-id="379ee-136">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="379ee-137">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="379ee-137">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="379ee-138">È possibile trovare l'URI ID app da usare nella descrizione della registrazione dell'app provider OIDC.</span><span class="sxs-lookup"><span data-stu-id="379ee-138">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
