---
title: ASP.NET Core Blazor WebAssembly scenari di sicurezza aggiuntivi
author: guardrex
description: Informazioni su come configurare Blazor WebAssembly per altri scenari di sicurezza.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: 79f7b2177d6d07101c73cde841c062b0e1468593
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445151"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="53b88-103">ASP.NET Core Blazor WebAssembly scenari di sicurezza aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="53b88-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="53b88-104">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="53b88-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="53b88-105">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="53b88-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="53b88-106">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio può essere usato con <xref:System.Net.Http.HttpClient> per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="53b88-106">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="53b88-107">I token vengono acquisiti utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> servizio esistente.</span><span class="sxs-lookup"><span data-stu-id="53b88-107">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="53b88-108">Se non è possibile acquisire un token, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="53b88-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="53b88-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>dispone di un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="53b88-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="53b88-110">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="53b88-110">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="53b88-111">Usare uno degli approcci seguenti per configurare un gestore di messaggi per le richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="53b88-111">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="53b88-112">[ `AuthorizationMessageHandler` Classe personalizzata](#custom-authorizationmessagehandler-class) (*scelta consigliata*)</span><span class="sxs-lookup"><span data-stu-id="53b88-112">[Custom `AuthorizationMessageHandler` class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="53b88-113">Configurare`AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="53b88-113">Configure `AuthorizationMessageHandler`</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="53b88-114">Classe AuthorizationMessageHandler personalizzata</span><span class="sxs-lookup"><span data-stu-id="53b88-114">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="53b88-115">Nell'esempio seguente viene estesa una classe personalizzata <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> che può essere usata per configurare un oggetto <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="53b88-115">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="53b88-116">In `Program.Main` ( `Program.cs` ), un oggetto <xref:System.Net.Http.HttpClient> viene configurato con il gestore dei messaggi di autorizzazione personalizzati:</span><span class="sxs-lookup"><span data-stu-id="53b88-116">In `Program.Main` (`Program.cs`), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="53b88-117">Per un' Blazor applicazione basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) può essere assegnato a <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-117">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="53b88-118">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene usato per effettuare richieste autorizzate usando il [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modello.</span><span class="sxs-lookup"><span data-stu-id="53b88-118">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="53b88-119">Se il client viene creato con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacchetto), <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste all'API del server:</span><span class="sxs-lookup"><span data-stu-id="53b88-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
        
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="53b88-120">Configurare AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="53b88-120">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="53b88-121">Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un <xref:System.Net.Http.HttpClient> in `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-121">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="53b88-122">Per un' Blazor app basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> può essere assegnato a:</span><span class="sxs-lookup"><span data-stu-id="53b88-122">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="53b88-123">Oggetto <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-123">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="53b88-124">URL della `authorizedUrls` matrice.</span><span class="sxs-lookup"><span data-stu-id="53b88-124">A URL of the `authorizedUrls` array.</span></span>

<span data-ttu-id="53b88-125">Per praticità, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="53b88-125">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="53b88-126">I modelli abilitati Blazor WebAssembly per l'autenticazione usano <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacchetto) nel progetto API server per configurare un <xref:System.Net.Http.HttpClient> con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="53b88-126">The authentication-enabled Blazor WebAssembly templates use <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="53b88-127">Per un' Blazor applicazione basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) può essere assegnato a <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-127">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="53b88-128">Se il client viene creato con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> nell'esempio precedente, <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="53b88-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="53b88-129">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene usato per effettuare richieste autorizzate usando il [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) modello:</span><span class="sxs-lookup"><span data-stu-id="53b88-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Client.GetFromJsonAsync<ExampleType[]>("{API METHOD}");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="53b88-130">HttpClient tipizzato</span><span class="sxs-lookup"><span data-stu-id="53b88-130">Typed HttpClient</span></span>

<span data-ttu-id="53b88-131">È possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe.</span><span class="sxs-lookup"><span data-stu-id="53b88-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="53b88-132">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="53b88-132">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient client;
 
    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="53b88-133">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `using static BlazorSample.Data;` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-133">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="53b88-134">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="53b88-134">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="53b88-135">Per un' Blazor applicazione basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) può essere assegnato a <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-135">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="53b88-136">`FetchData`componente ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-136">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="53b88-137">Configurare il gestore HttpClient</span><span class="sxs-lookup"><span data-stu-id="53b88-137">Configure the HttpClient handler</span></span>

<span data-ttu-id="53b88-138">Il gestore può essere configurato ulteriormente con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="53b88-138">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="53b88-139">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="53b88-139">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="53b88-140">Per un' Blazor app basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> può essere assegnato a:</span><span class="sxs-lookup"><span data-stu-id="53b88-140">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> can be assigned to:</span></span>

* <span data-ttu-id="53b88-141">Oggetto <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-141">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="53b88-142">URL della `authorizedUrls` matrice.</span><span class="sxs-lookup"><span data-stu-id="53b88-142">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="53b88-143">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="53b88-143">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="53b88-144">Se l' Blazor WebAssembly app usa normalmente un valore predefinito sicuro <xref:System.Net.Http.HttpClient> , l'app può anche eseguire richieste API Web non autenticate o non autorizzate configurando un oggetto denominato <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="53b88-144">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="53b88-145">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="53b88-145">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="53b88-146">Per un' Blazor applicazione basata sul Blazor WebAssembly modello ospitato, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `new Uri(builder.HostEnvironment.BaseAddress)` ) può essere assegnato a <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-146">For a Blazor app based on the Blazor WebAssembly Hosted template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) can be assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="53b88-147">La registrazione precedente è aggiunta alla registrazione predefinita protetta esistente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="53b88-147">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="53b88-148">Un componente crea <xref:System.Net.Http.HttpClient> dalla <xref:System.Net.Http.IHttpClientFactory> ( [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) pacchetto) per eseguire richieste non autenticate o non autorizzate:</span><span class="sxs-lookup"><span data-stu-id="53b88-148">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="53b88-149">Il controller nell'API del server, `WeatherForecastNoAuthenticationController` per l'esempio precedente, non è contrassegnato con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="53b88-149">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="53b88-150">La decisione di usare un client protetto o un client non sicuro come <xref:System.Net.Http.HttpClient> istanza predefinita è spetta allo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="53b88-150">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="53b88-151">Un modo per prendere questa decisione consiste nel considerare il numero di endpoint autenticati e non autenticati contattati dall'app.</span><span class="sxs-lookup"><span data-stu-id="53b88-151">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="53b88-152">Se la maggior parte delle richieste dell'app è quella di proteggere gli endpoint dell'API, usare l' <xref:System.Net.Http.HttpClient> istanza autenticata come predefinita.</span><span class="sxs-lookup"><span data-stu-id="53b88-152">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="53b88-153">In caso contrario, registrare l'istanza non autenticata <xref:System.Net.Http.HttpClient> come predefinita.</span><span class="sxs-lookup"><span data-stu-id="53b88-153">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="53b88-154">Un approccio alternativo all'utilizzo di <xref:System.Net.Http.IHttpClientFactory> consiste nel creare un [client tipizzato](#typed-httpclient) per l'accesso non autenticato agli endpoint anonimi.</span><span class="sxs-lookup"><span data-stu-id="53b88-154">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="53b88-155">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="53b88-155">Request additional access tokens</span></span>

<span data-ttu-id="53b88-156">I token di accesso possono essere ottenuti manualmente chiamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="53b88-156">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="53b88-157">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="53b88-157">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="53b88-158">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app client.</span><span class="sxs-lookup"><span data-stu-id="53b88-158">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="53b88-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="53b88-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

<span data-ttu-id="53b88-160">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti.</span><span class="sxs-lookup"><span data-stu-id="53b88-160">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="53b88-161">In un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="53b88-161">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="53b88-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Restituisce</span><span class="sxs-lookup"><span data-stu-id="53b88-162"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="53b88-163">`true`con l'oggetto `token` da usare.</span><span class="sxs-lookup"><span data-stu-id="53b88-163">`true` with the `token` for use.</span></span>
* <span data-ttu-id="53b88-164">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="53b88-164">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="53b88-165">HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch</span><span class="sxs-lookup"><span data-stu-id="53b88-165">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="53b88-166">Quando è in esecuzione in un webassembly in un' Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([documentazione API](xref:System.Net.Http.HttpClient)) e <xref:System.Net.Http.HttpRequestMessage> può essere usato per personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="53b88-166">When running on WebAssembly in a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) ([API documentation](xref:System.Net.Http.HttpClient)) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="53b88-167">Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta.</span><span class="sxs-lookup"><span data-stu-id="53b88-167">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="53b88-168">Il componente seguente effettua una `POST` richiesta a un endpoint API to do list nel server e Mostra il corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="53b88-168">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

```razor
@page "/todorequest"
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http
@inject IAccessTokenProvider TokenProvider

<h1>ToDo Request</h1>

<button @onclick="PostRequest">Submit POST request</button>

<p>Response body returned by the server:</p>

<p>@responseBody</p>

@code {
    private string responseBody;

    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content =
                JsonContent.Create(new TodoItem
                {
                    Name = "My New Todo Item",
                    IsComplete = false
                })
        };

        var tokenResult = await TokenProvider.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            requestMessage.Headers.Authorization =
                new AuthenticationHeaderValue("Bearer", token.Value);

            requestMessage.Content.Headers.TryAddWithoutValidation(
                "x-custom-header", "value");

            var response = await Http.SendAsync(requestMessage);
            var responseStatusCode = response.StatusCode;

            responseBody = await response.Content.ReadAsStringAsync();
        }
    }

    public class TodoItem
    {
        public long Id { get; set; }
        public string Name { get; set; }
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="53b88-169">L'implementazione di .NET webassembly <xref:System.Net.Http.HttpClient> Usa [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="53b88-169">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="53b88-170">Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="53b88-170">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="53b88-171">Le opzioni di richiesta di recupero HTTP possono essere configurate con <xref:System.Net.Http.HttpRequestMessage> i metodi di estensione illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="53b88-171">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="53b88-172">Metodo di estensione</span><span class="sxs-lookup"><span data-stu-id="53b88-172">Extension method</span></span> | <span data-ttu-id="53b88-173">Recupera la proprietà della richiesta</span><span class="sxs-lookup"><span data-stu-id="53b88-173">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="53b88-174">È possibile impostare opzioni aggiuntive usando il metodo di <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> estensione più generico.</span><span class="sxs-lookup"><span data-stu-id="53b88-174">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="53b88-175">La risposta HTTP viene in genere memorizzata nel buffer in un' Blazor WebAssembly applicazione per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="53b88-175">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="53b88-176">Per abilitare il supporto per il flusso di risposta, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodo di estensione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="53b88-176">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="53b88-177">Per includere le credenziali in una richiesta tra più origini, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="53b88-177">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="53b88-178">Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="53b88-178">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="53b88-179">Condivisione di risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="53b88-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="53b88-180">Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l' `Authorization` intestazione deve essere consentita dal criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="53b88-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="53b88-181">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="53b88-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="53b88-182">Origin della richiesta ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="53b88-183">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="53b88-183">Any method (verb).</span></span>
* <span data-ttu-id="53b88-184">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="53b88-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="53b88-185">Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header` ), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="53b88-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="53b88-186">Credenziali impostate dal codice JavaScript lato client ( `credentials` proprietà impostata su `include` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="53b88-187">Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richieste HTTP dell'app di esempio ( `Components/HTTPRequestTester.razor` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="53b88-188">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="53b88-188">Handle token request errors</span></span>

<span data-ttu-id="53b88-189">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel Identity provider (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le credenziali.</span><span class="sxs-lookup"><span data-stu-id="53b88-189">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="53b88-190">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="53b88-190">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="53b88-191">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="53b88-191">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="53b88-192">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="53b88-192">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="53b88-193">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="53b88-193">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="53b88-194">Questo scenario si verifica se un utente visita `https://login.microsoftonline.com` e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="53b88-194">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="53b88-195">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="53b88-195">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="53b88-196">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="53b88-196">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="53b88-197">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="53b88-197">They are part of the nature of SPAs.</span></span> <span data-ttu-id="53b88-198">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="53b88-198">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="53b88-199">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="53b88-199">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="53b88-200">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-200">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="53b88-201">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="53b88-201">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="53b88-202">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="53b88-202">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="53b88-203">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="53b88-203">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="53b88-204">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="53b88-204">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="53b88-205">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="53b88-205">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="53b88-206">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="53b88-206">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="53b88-207">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="53b88-207">Store the current page state in session storage.</span></span> <span data-ttu-id="53b88-208">Durante l' [ `OnInitializedAsync` evento del ciclo](xref:blazor/components/lifecycle#component-initialization-methods) di vita ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verificare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="53b88-208">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="53b88-209">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="53b88-209">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="53b88-210">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="53b88-210">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="53b88-211">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="53b88-211">The following example shows how to:</span></span>

* <span data-ttu-id="53b88-212">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="53b88-212">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="53b88-213">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="53b88-213">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="53b88-214">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="53b88-214">Save app state before an authentication operation</span></span>

<span data-ttu-id="53b88-215">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="53b88-215">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="53b88-216">Questo può avvenire quando si usa un contenitore di stato e si vuole ripristinare lo stato dopo che l'autenticazione ha avuto esito positivo.</span><span class="sxs-lookup"><span data-stu-id="53b88-216">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="53b88-217">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="53b88-217">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="53b88-218">Nell'esempio seguente viene illustrato l'approccio.</span><span class="sxs-lookup"><span data-stu-id="53b88-218">The following example demonstrates the approach.</span></span>

<span data-ttu-id="53b88-219">Viene creata una classe contenitore di stato nell'app con proprietà che contengono i valori di stato dell'app.</span><span class="sxs-lookup"><span data-stu-id="53b88-219">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="53b88-220">Nell'esempio seguente il contenitore viene usato per gestire il valore del contatore del componente del modello predefinito `Counter` ( `Pages/Counter.razor` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-220">In the following example, the container is used to maintain the counter value of the default template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="53b88-221">I metodi per la serializzazione e la deserializzazione del contenitore sono basati su <xref:System.Text.Json> .</span><span class="sxs-lookup"><span data-stu-id="53b88-221">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="53b88-222">Il `Counter` componente usa il contenitore di stato per mantenere il `currentCount` valore all'esterno del componente:</span><span class="sxs-lookup"><span data-stu-id="53b88-222">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="53b88-223">Creare un `ApplicationAuthenticationState` da <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> .</span><span class="sxs-lookup"><span data-stu-id="53b88-223">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="53b88-224">Fornire una `Id` proprietà, che funge da identificatore per lo stato archiviato localmente.</span><span class="sxs-lookup"><span data-stu-id="53b88-224">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="53b88-225">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="53b88-225">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="53b88-226">Il `Authentication` componente ( `Pages/Authentication.razor` ) Salva e ripristina lo stato dell'app usando l'archiviazione di sessione locale con i `StateContainer` metodi di serializzazione e deserializzazione `GetStateForLocalStorage` e `SetStateFromLocalStorage` :</span><span class="sxs-lookup"><span data-stu-id="53b88-226">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="53b88-227">Questo esempio USA Azure Active Directory (AAD) per l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-227">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="53b88-228">In `Program.Main` ( `Program.cs` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-228">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="53b88-229">`ApplicationAuthenticationState`Viene configurato come tipo di libreria Microsoft autenticazione (MSAL) `RemoteAuthenticationState` .</span><span class="sxs-lookup"><span data-stu-id="53b88-229">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="53b88-230">Il contenitore di stato è registrato nel contenitore dei servizi.</span><span class="sxs-lookup"><span data-stu-id="53b88-230">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="53b88-231">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="53b88-231">Customize app routes</span></span>

<span data-ttu-id="53b88-232">Per impostazione predefinita, la [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) libreria usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="53b88-232">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="53b88-233">Route</span><span class="sxs-lookup"><span data-stu-id="53b88-233">Route</span></span>                            | <span data-ttu-id="53b88-234">Scopo</span><span class="sxs-lookup"><span data-stu-id="53b88-234">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="53b88-235">Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="53b88-235">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="53b88-236">Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="53b88-236">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="53b88-237">Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="53b88-237">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="53b88-238">Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="53b88-238">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="53b88-239">Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="53b88-239">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="53b88-240">Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="53b88-240">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="53b88-241">Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="53b88-241">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="53b88-242">Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="53b88-242">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="53b88-243">Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="53b88-243">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="53b88-244">Le route visualizzate nella tabella precedente sono configurabili tramite <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-244">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="53b88-245">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="53b88-245">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="53b88-246">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security` .</span><span class="sxs-lookup"><span data-stu-id="53b88-246">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="53b88-247">`Authentication`componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-247">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="53b88-248">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="53b88-248">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="53b88-249">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="53b88-249">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="53b88-250">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="53b88-250">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="53b88-251">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="53b88-251">Customize the authentication user interface</span></span>

<span data-ttu-id="53b88-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-252"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="53b88-253">Ogni stato può essere personalizzato passando un oggetto personalizzato <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="53b88-253">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="53b88-254">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="53b88-254">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="53b88-255">`Authentication`componente ( `Pages/Authentication.razor` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-255">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="53b88-256"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="53b88-256">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="53b88-257">Route</span><span class="sxs-lookup"><span data-stu-id="53b88-257">Route</span></span>                            | <span data-ttu-id="53b88-258">Frammento</span><span class="sxs-lookup"><span data-stu-id="53b88-258">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="53b88-259">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="53b88-259">Customize the user</span></span>

<span data-ttu-id="53b88-260">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="53b88-260">Users bound to the app can be customized.</span></span> <span data-ttu-id="53b88-261">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="53b88-261">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="53b88-262">Creare una classe che estende la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="53b88-262">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="53b88-263">Creare una factory che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="53b88-263">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);
        
        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }
           
        return initialUser;
    }
}
```

<span data-ttu-id="53b88-264">Registrare `CustomAccountFactory` per il provider di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="53b88-264">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="53b88-265">Una delle seguenti registrazioni è valida:</span><span class="sxs-lookup"><span data-stu-id="53b88-265">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="53b88-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="53b88-266"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="53b88-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="53b88-267"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="53b88-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="53b88-268"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
  {
      ...
  })
  .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
      CustomUserAccount, CustomAccountFactory>();
  ```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="53b88-269">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="53b88-269">Support prerendering with authentication</span></span>

<span data-ttu-id="53b88-270">Dopo aver seguito le indicazioni in uno degli argomenti dell'app ospitata Blazor WebAssembly , usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="53b88-270">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="53b88-271">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-271">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="53b88-272">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-272">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="53b88-273">Nella `Program` classe () dell'app client `Program.cs` , fattorizzare le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="53b88-273">In the Client app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="53b88-274">Nell'app Server `Startup.ConfigureServices` registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="53b88-274">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="53b88-275">Nel metodo dell'app Server `Startup.Configure` sostituire [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) con [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A) :</span><span class="sxs-lookup"><span data-stu-id="53b88-275">In the Server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="53b88-276">Nell'app Server creare una `Pages` cartella se non esiste.</span><span class="sxs-lookup"><span data-stu-id="53b88-276">In the Server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="53b88-277">Creare una `_Host.cshtml` pagina all'interno della cartella dell'app Server `Pages` .</span><span class="sxs-lookup"><span data-stu-id="53b88-277">Create a `_Host.cshtml` page inside the Server app's `Pages` folder.</span></span> <span data-ttu-id="53b88-278">Incollare il contenuto del file dell'app client `wwwroot/index.html` nel `Pages/_Host.cshtml` file.</span><span class="sxs-lookup"><span data-stu-id="53b88-278">Paste the contents from the Client app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="53b88-279">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="53b88-279">Update the file's contents:</span></span>

* <span data-ttu-id="53b88-280">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="53b88-280">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="53b88-281">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="53b88-281">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="53b88-282">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="53b88-282">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="53b88-283">Quando si esegue l'autenticazione e l'autorizzazione di un'app ospitata Blazor WebAssembly con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="53b88-283">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="53b88-284">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="53b88-284">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="53b88-285">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="53b88-285">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="53b88-286">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="53b88-286">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="53b88-287">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="53b88-287">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="53b88-288">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="53b88-288">In this scenario:</span></span>

* <span data-ttu-id="53b88-289">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="53b88-289">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="53b88-290">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="53b88-290">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="53b88-291">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="53b88-291">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="53b88-292">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="53b88-292">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="53b88-293">Configurare Identity con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="53b88-293">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="53b88-294">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="53b88-294">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="53b88-295">Quando un utente esegue Identity l'accesso, raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="53b88-295">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="53b88-296">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="53b88-296">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="53b88-297">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="53b88-297">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="53b88-298">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="53b88-298">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="53b88-299">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente da Identity nel client.</span><span class="sxs-lookup"><span data-stu-id="53b88-299">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="53b88-300">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="53b88-300">We don't recommend this approach.</span></span> <span data-ttu-id="53b88-301">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="53b88-301">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="53b88-302">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="53b88-302">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="53b88-303">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="53b88-303">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="53b88-304">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="53b88-304">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="53b88-305">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="53b88-305">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="53b88-306">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="53b88-306">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="53b88-307">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="53b88-307">Make an API call from the client to the server API.</span></span> <span data-ttu-id="53b88-308">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="53b88-308">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="53b88-309">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="53b88-309">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="53b88-310">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="53b88-310">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="53b88-311">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="53b88-311">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="53b88-312">Usa endpoint Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="53b88-312">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="53b88-313">I modelli e la libreria di autenticazione Blazor usano gli endpoint Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="53b88-313">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="53b88-314">Per usare un endpoint v 2.0, configurare l'opzione JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="53b88-314">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="53b88-315">Nell'esempio seguente AAD è configurato per la versione 2.0 aggiungendo un `v2.0` segmento alla <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> proprietà:</span><span class="sxs-lookup"><span data-stu-id="53b88-315">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="53b88-316">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app ( `appsettings.json` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-316">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="53b88-317">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="53b88-317">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="53b88-318">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o nel file di impostazioni dell'app ( `appsettings.json` ) con la `Authority` chiave.</span><span class="sxs-lookup"><span data-stu-id="53b88-318">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="53b88-319">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="53b88-319">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="53b88-320">Per ulteriori informazioni, vedere la pagina relativa [all'aggiornamento alla piattaforma di identità Microsoft (v 2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="53b88-320">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="53b88-321">Configurare e usare gRPC nei componenti</span><span class="sxs-lookup"><span data-stu-id="53b88-321">Configure and use gRPC in components</span></span>

<span data-ttu-id="53b88-322">Per configurare un' Blazor WebAssembly app per l'uso del [framework ASP.NET Core gRPC](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="53b88-322">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="53b88-323">Abilitare gRPC-Web sul server.</span><span class="sxs-lookup"><span data-stu-id="53b88-323">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="53b88-324">Per altre informazioni, vedere <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="53b88-324">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="53b88-325">Registrare i servizi di gRPC per il gestore di messaggi dell'app.</span><span class="sxs-lookup"><span data-stu-id="53b88-325">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="53b88-326">Nell'esempio seguente viene configurato il gestore dei messaggi di autorizzazione dell'app per usare il [ `GreeterClient` servizio dell'esercitazione su gRPC](xref:tutorials/grpc/grpc-start#create-a-grpc-service) ( `Program.Main` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-326">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="53b88-327">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-327">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="53b88-328">Inserire il `.proto` file nel `Shared` progetto della soluzione ospitata Blazor .</span><span class="sxs-lookup"><span data-stu-id="53b88-328">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="53b88-329">Un componente nell'app client può effettuare chiamate gRPC usando il client gRPC ( `Pages/Grpc.razor` ):</span><span class="sxs-lookup"><span data-stu-id="53b88-329">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="53b88-330">Il segnaposto `{APP ASSEMBLY}` è il nome dell'assembly dell'app (ad esempio, `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="53b88-330">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="53b88-331">Per usare la `Status.DebugException` proprietà, usare [Grpc .NET. client](https://www.nuget.org/packages/Grpc.Net.Client) versione 2.30.0 o successiva.</span><span class="sxs-lookup"><span data-stu-id="53b88-331">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="53b88-332">Per altre informazioni, vedere <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="53b88-332">For more information, see <xref:grpc/browser>.</span></span>
