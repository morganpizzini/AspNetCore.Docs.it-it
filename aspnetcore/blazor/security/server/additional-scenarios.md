---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: scenari di sicurezza aggiuntivi'
author: guardrex
description: 'Informazioni su come configurare :::no-loc(Blazor Server)::: per altri scenari di sicurezza.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: ac30b2ba9da4b5dbc2e02a2f6eb1252927483f73
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055503"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="263f1-103">ASP.NET Core :::no-loc(Blazor Server)::: scenari di sicurezza aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="263f1-103">ASP.NET Core :::no-loc(Blazor Server)::: additional security scenarios</span></span>

<span data-ttu-id="263f1-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="263f1-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="263f1-105">Passare i token a un' :::no-loc(Blazor Server)::: app</span><span class="sxs-lookup"><span data-stu-id="263f1-105">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="263f1-106">I token disponibili all'esterno dei :::no-loc(Razor)::: componenti di un' :::no-loc(Blazor Server)::: app possono essere passati ai componenti con l'approccio descritto in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="263f1-106">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="263f1-107">Autenticare l' :::no-loc(Blazor Server)::: app come se si trattasse di un normale :::no-loc(Razor)::: sito o di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="263f1-107">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="263f1-108">Eseguire il provisioning e salvare i token nell'autenticazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="263f1-108">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="263f1-109">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="263f1-109">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="263f1-110">Facoltativamente, viene aggiunto un ambito aggiuntivo con `options.Scope.Add("{SCOPE}");` , in cui il segnaposto `{SCOPE}` è l'ambito aggiuntivo da aggiungere.</span><span class="sxs-lookup"><span data-stu-id="263f1-110">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="263f1-111">Definire un servizio del provider di token con **ambito** che può essere usato nell' :::no-loc(Blazor)::: app per risolvere i token da [inserimento delle dipendenze](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="263f1-111">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="263f1-112">In `Startup.ConfigureServices` aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="263f1-112">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="263f1-113">Definire una classe per passare lo stato iniziale dell'app con i token di accesso e di aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="263f1-113">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="263f1-114">Nel `_Host.cshtml` file creare un'istanza di e `InitialApplicationState` passarla come parametro all'app:</span><span class="sxs-lookup"><span data-stu-id="263f1-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="263f1-115">Nel `App` componente ( `App.razor` ) risolvere il servizio e inizializzarlo con i dati del parametro:</span><span class="sxs-lookup"><span data-stu-id="263f1-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="263f1-116">Aggiungere un riferimento al pacchetto all'app per il [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="263f1-116">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="263f1-117">Nel servizio che esegue una richiesta API protetta, inserire il provider di token e recuperare il token per la richiesta dell'API:</span><span class="sxs-lookup"><span data-stu-id="263f1-117">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="263f1-118">Impostare lo schema di autenticazione</span><span class="sxs-lookup"><span data-stu-id="263f1-118">Set the authentication scheme</span></span></h2>

<span data-ttu-id="263f1-119">Per un'app che usa più di un middleware di autenticazione e pertanto dispone di più di uno schema di autenticazione, lo schema :::no-loc(Blazor)::: usato da può essere impostato in modo esplicito nella configurazione dell'endpoint di `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="263f1-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="263f1-120">Nell'esempio seguente viene impostato lo schema di Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="263f1-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="263f1-121">Passare i token a un' :::no-loc(Blazor Server)::: app</span><span class="sxs-lookup"><span data-stu-id="263f1-121">Pass tokens to a :::no-loc(Blazor Server)::: app</span></span></h2>

<span data-ttu-id="263f1-122">I token disponibili all'esterno dei :::no-loc(Razor)::: componenti di un' :::no-loc(Blazor Server)::: app possono essere passati ai componenti con l'approccio descritto in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="263f1-122">Tokens available outside of the :::no-loc(Razor)::: components in a :::no-loc(Blazor Server)::: app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="263f1-123">Autenticare l' :::no-loc(Blazor Server)::: app come se si trattasse di un normale :::no-loc(Razor)::: sito o di un'app MVC.</span><span class="sxs-lookup"><span data-stu-id="263f1-123">Authenticate the :::no-loc(Blazor Server)::: app as you would with a regular :::no-loc(Razor)::: Pages or MVC app.</span></span> <span data-ttu-id="263f1-124">Eseguire il provisioning e salvare i token nell'autenticazione :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="263f1-124">Provision and save the tokens to the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="263f1-125">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="263f1-125">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.:::no-loc(Identity):::Model.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="263f1-126">Facoltativamente, viene aggiunto un ambito aggiuntivo con `options.Scope.Add("{SCOPE}");` , in cui il segnaposto `{SCOPE}` è l'ambito aggiuntivo da aggiungere.</span><span class="sxs-lookup"><span data-stu-id="263f1-126">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="263f1-127">Facoltativamente, la risorsa viene specificata con `options.Resource = "{RESOURCE}";` , dove il segnaposto `{RESOURCE}` è la risorsa.</span><span class="sxs-lookup"><span data-stu-id="263f1-127">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="263f1-128">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="263f1-128">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="263f1-129">Definire una classe per passare lo stato iniziale dell'app con i token di accesso e di aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="263f1-129">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="263f1-130">Definire un servizio del provider di token con **ambito** che può essere usato nell' :::no-loc(Blazor)::: app per risolvere i token da [inserimento delle dipendenze](xref:blazor/fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="263f1-130">Define a **scoped** token provider service that can be used within the :::no-loc(Blazor)::: app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="263f1-131">In `Startup.ConfigureServices` aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="263f1-131">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="263f1-132">Nel `_Host.cshtml` file creare un'istanza di e `InitialApplicationState` passarla come parametro all'app:</span><span class="sxs-lookup"><span data-stu-id="263f1-132">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="263f1-133">Nel `App` componente ( `App.razor` ) risolvere il servizio e inizializzarlo con i dati del parametro:</span><span class="sxs-lookup"><span data-stu-id="263f1-133">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="263f1-134">Aggiungere un riferimento al pacchetto all'app per il [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="263f1-134">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="263f1-135">Nel servizio che esegue una richiesta API protetta, inserire il provider di token e recuperare il token per la richiesta dell'API:</span><span class="sxs-lookup"><span data-stu-id="263f1-135">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient client;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        client = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="263f1-136">Impostare lo schema di autenticazione</span><span class="sxs-lookup"><span data-stu-id="263f1-136">Set the authentication scheme</span></span></h2>

<span data-ttu-id="263f1-137">Per un'app che usa più di un middleware di autenticazione e pertanto dispone di più di uno schema di autenticazione, lo schema :::no-loc(Blazor)::: usato da può essere impostato in modo esplicito nella configurazione dell'endpoint di `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="263f1-137">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that :::no-loc(Blazor)::: uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="263f1-138">Nell'esempio seguente viene impostato lo schema di Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="263f1-138">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.Map:::no-loc(Blazor):::Hub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="263f1-139">Usare gli endpoint OpenID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="263f1-139">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="263f1-140">Nelle versioni di ASP.NET Core precedenti alla 5,0, i modelli e la libreria di autenticazione :::no-loc(Blazor)::: usano gli endpoint OpenID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="263f1-140">In versions of ASP.NET Core prior to 5.0, the authentication library and :::no-loc(Blazor)::: templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="263f1-141">Per usare un endpoint v 2.0 con versioni di ASP.NET Core precedenti alla 5,0, configurare l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opzione nel <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="263f1-141">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="263f1-142">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app ( `:::no-loc(appsettings.json):::` ):</span><span class="sxs-lookup"><span data-stu-id="263f1-142">Alternatively, the setting can be made in the app settings (`:::no-loc(appsettings.json):::`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="263f1-143">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="263f1-143">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="263f1-144">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o nel file di impostazioni dell'app con la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> chiave.</span><span class="sxs-lookup"><span data-stu-id="263f1-144">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="263f1-145">Modifiche al codice</span><span class="sxs-lookup"><span data-stu-id="263f1-145">Code changes</span></span>

* <span data-ttu-id="263f1-146">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="263f1-146">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="263f1-147">Per ulteriori informazioni, vedere [perché eseguire l'aggiornamento alla piattaforma di identità Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="263f1-147">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="263f1-148">nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="263f1-148">in the Azure documentation.</span></span>
* <span data-ttu-id="263f1-149">Poiché le risorse vengono specificate negli URI di ambito per gli endpoint v 2.0, rimuovere l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> impostazione della proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="263f1-149">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="263f1-150">Per altre informazioni, vedere [ambiti, non risorse](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="263f1-150">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="263f1-151">URI ID app</span><span class="sxs-lookup"><span data-stu-id="263f1-151">App ID URI</span></span>

* <span data-ttu-id="263f1-152">Quando si usano gli endpoint v 2.0, le API definiscono un oggetto *`App ID URI`* , che è destinato a rappresentare un identificatore univoco per l'API.</span><span class="sxs-lookup"><span data-stu-id="263f1-152">When using v2.0 endpoints, APIs define an *`App ID URI`* , which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="263f1-153">Tutti gli ambiti includono l'URI ID app come prefisso, mentre gli endpoint v 2.0 emettono token di accesso con l'URI ID app come destinatari.</span><span class="sxs-lookup"><span data-stu-id="263f1-153">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="263f1-154">Quando si usano gli endpoint V 2.0, l'ID client configurato nell'API del server cambia dall'ID applicazione (ID client) dell'API all'URI ID app.</span><span class="sxs-lookup"><span data-stu-id="263f1-154">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="263f1-155">`:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="263f1-155">`:::no-loc(appsettings.json):::`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="263f1-156">È possibile trovare l'URI ID app da usare nella descrizione della registrazione dell'app provider OIDC.</span><span class="sxs-lookup"><span data-stu-id="263f1-156">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
