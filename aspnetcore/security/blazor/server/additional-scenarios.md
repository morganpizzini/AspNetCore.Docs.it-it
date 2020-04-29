---
title: Scenari Blazor di sicurezza aggiuntivi di ASP.NET Core Server
author: guardrex
description: Informazioni su come configurare Blazor il server per altri scenari di sicurezza.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/additional-scenarios
ms.openlocfilehash: 1a3e5a215daedbb9b97c1924275701915806983e
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206389"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="e3ca5-103">Scenari Blazor di sicurezza aggiuntivi di ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="e3ca5-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="e3ca5-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e3ca5-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="e3ca5-105">Passare i token a un' Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="e3ca5-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="e3ca5-106">I token disponibili all'esterno dei componenti Razor in un' Blazor app Server possono essere passati ai componenti con l'approccio descritto in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="e3ca5-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="e3ca5-107">Per il codice di esempio, incluso `Startup.ConfigureServices` un esempio completo, vedere il [passaggio di token a un'applicazione Blazor lato server](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="e3ca5-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="e3ca5-108">Autenticare l' Blazor app Server come si farebbe con una normale app Razor Pages o MVC.</span><span class="sxs-lookup"><span data-stu-id="e3ca5-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="e3ca5-109">Eseguire il provisioning e salvare i token nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e3ca5-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="e3ca5-110">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-110">For example:</span></span>

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

<span data-ttu-id="e3ca5-111">Definire una classe per passare lo stato iniziale dell'app con i token di accesso e di aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="e3ca5-112">Definire un servizio del provider di token con **ambito** che può essere usato Blazor nell'app per risolvere i token da [inserimento delle dipendenze](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="e3ca5-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="e3ca5-113">In `Startup.ConfigureServices`aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="e3ca5-114">Nel file *_Host. cshtml* creare un'istanza di `InitialApplicationState` e passarla come parametro all'app:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-114">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="e3ca5-115">Nel `App` componente (*app. Razor*) risolvere il servizio e inizializzarlo con i dati del parametro:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-115">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="e3ca5-116">Nel servizio che esegue una richiesta API protetta, inserire il provider di token e recuperare il token per chiamare l'API:</span><span class="sxs-lookup"><span data-stu-id="e3ca5-116">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```
