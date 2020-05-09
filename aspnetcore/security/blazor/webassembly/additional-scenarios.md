---
title: Scenari Blazor di sicurezza aggiuntivi ASP.NET Core webassembly
author: guardrex
description: Informazioni su come configurare Blazor webassembly per altri scenari di sicurezza.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: e8a088b3f1a4e0eb7d5d1c5c09ef53c4a2bd3628
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976792"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="eedfb-103">Scenari di sicurezza aggiuntivi del webassembly ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="eedfb-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="eedfb-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="eedfb-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="eedfb-105">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="eedfb-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="eedfb-106">Il `AuthorizationMessageHandler` servizio può essere usato con `HttpClient` per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="eedfb-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="eedfb-107">I token vengono acquisiti utilizzando il servizio `IAccessTokenProvider` esistente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="eedfb-108">Se non è possibile acquisire un token, `AccessTokenNotAvailableException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="eedfb-109">`AccessTokenNotAvailableException`dispone di `Redirect` un metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="eedfb-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="eedfb-110">Il `AuthorizationMessageHandler` può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati `ConfigureHandler` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="eedfb-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="eedfb-111">Nell' `AuthorizationMessageHandler` esempio seguente configura `HttpClient` un in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="eedfb-112">Per praticità, `BaseAddressAuthorizationMessageHandler` è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="eedfb-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="eedfb-113">I modelli webassembly di Blazer abilitati per l' <xref:System.Net.Http.IHttpClientFactory> autenticazione ora usano nel progetto API server per configurare <xref:System.Net.Http.HttpClient> un con `BaseAddressAuthorizationMessageHandler`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="eedfb-114">Se il client viene creato con `CreateClient` nell'esempio precedente, vengono fornite <xref:System.Net.Http.HttpClient> le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="eedfb-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="eedfb-115">L'oggetto <xref:System.Net.Http.HttpClient> configurato viene quindi usato per effettuare richieste autorizzate usando `try-catch` un modello semplice.</span><span class="sxs-lookup"><span data-stu-id="eedfb-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="eedfb-116">Il componente `FetchData` seguente richiede i dati delle previsioni meteo:</span><span class="sxs-lookup"><span data-stu-id="eedfb-116">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

<span data-ttu-id="eedfb-117">In alternativa, è possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe:</span><span class="sxs-lookup"><span data-stu-id="eedfb-117">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="eedfb-118">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="eedfb-118">*WeatherClient.cs*:</span></span>

```csharp
using System.Collections.Generic;
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherClient
{
    private readonly HttpClient httpClient;
 
    public WeatherClient(HttpClient httpClient)
    {
        this.httpClient = httpClient;
    }
 
    public async Task<IEnumerable<WeatherForecast>> GetWeatherForeacasts()
    {
        IEnumerable<WeatherForecast> forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");

            ...
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="eedfb-119">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eedfb-119">*Program.cs*:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="eedfb-120">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="eedfb-120">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="request-additional-access-tokens"></a><span data-ttu-id="eedfb-121">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="eedfb-121">Request additional access tokens</span></span>

<span data-ttu-id="eedfb-122">I token di accesso possono essere ottenuti manualmente `IAccessTokenProvider.RequestAccessToken`chiamando.</span><span class="sxs-lookup"><span data-stu-id="eedfb-122">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="eedfb-123">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="eedfb-123">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="eedfb-124">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app`Program.Main`client (, *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-124">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="eedfb-125">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="eedfb-125">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
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

<span data-ttu-id="eedfb-126">`TryGetToken`Restituisce</span><span class="sxs-lookup"><span data-stu-id="eedfb-126">`TryGetToken` returns:</span></span>

* <span data-ttu-id="eedfb-127">`true`con l' `token` oggetto da usare.</span><span class="sxs-lookup"><span data-stu-id="eedfb-127">`true` with the `token` for use.</span></span>
* <span data-ttu-id="eedfb-128">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="eedfb-128">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="eedfb-129">HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch</span><span class="sxs-lookup"><span data-stu-id="eedfb-129">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="eedfb-130">Quando è in esecuzione su webassembly in un'app webassembly blazer, <xref:System.Net.Http.HttpRequestMessage> [HttpClient](xref:fundamentals/http-requests) e può essere usato per personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="eedfb-130">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="eedfb-131">Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta.</span><span class="sxs-lookup"><span data-stu-id="eedfb-131">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="eedfb-132">Nell'esempio seguente viene eseguita `POST` una richiesta a un endpoint API to do list nel server e viene visualizzato il corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="eedfb-132">The following example makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<p>@_responseBody</p>

@code {
    private string _responseBody;

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

            _responseBody = await response.Content.ReadAsStringAsync();
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

<span data-ttu-id="eedfb-133">L'implementazione di `HttpClient` .NET webassembly USA [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="eedfb-133">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="eedfb-134">Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="eedfb-134">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="eedfb-135">Le opzioni di richiesta di recupero HTTP possono `HttpRequestMessage` essere configurate con i metodi di estensione illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-135">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="eedfb-136">`HttpRequestMessage`Metodo di estensione</span><span class="sxs-lookup"><span data-stu-id="eedfb-136">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="eedfb-137">Recupera la proprietà della richiesta</span><span class="sxs-lookup"><span data-stu-id="eedfb-137">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="eedfb-138">credenziali</span><span class="sxs-lookup"><span data-stu-id="eedfb-138">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="eedfb-139">cache</span><span class="sxs-lookup"><span data-stu-id="eedfb-139">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="eedfb-140">mode</span><span class="sxs-lookup"><span data-stu-id="eedfb-140">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="eedfb-141">integrità</span><span class="sxs-lookup"><span data-stu-id="eedfb-141">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="eedfb-142">È possibile impostare opzioni aggiuntive usando il metodo di `SetBrowserRequestOption` estensione più generico.</span><span class="sxs-lookup"><span data-stu-id="eedfb-142">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="eedfb-143">La risposta HTTP viene in genere memorizzata nel buffer in un'app webassembly blazer per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="eedfb-143">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="eedfb-144">Per abilitare il supporto per il flusso di risposta `SetBrowserResponseStreamingEnabled` , usare il metodo di estensione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="eedfb-144">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="eedfb-145">Per includere le credenziali in una richiesta tra più origini, usare `SetBrowserRequestCredentials` il metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="eedfb-145">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="eedfb-146">Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="eedfb-146">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="eedfb-147">Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle `Authorization` richieste CORS, l'intestazione deve essere consentita dal criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="eedfb-147">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="eedfb-148">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="eedfb-148">The following policy includes configuration for:</span></span>

* <span data-ttu-id="eedfb-149">Origin della richiesta`http://localhost:5000`( `https://localhost:5001`,).</span><span class="sxs-lookup"><span data-stu-id="eedfb-149">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="eedfb-150">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="eedfb-150">Any method (verb).</span></span>
* <span data-ttu-id="eedfb-151">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="eedfb-151">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="eedfb-152">Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header`), elencare l'intestazione quando <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>si chiama.</span><span class="sxs-lookup"><span data-stu-id="eedfb-152">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="eedfb-153">Credenziali impostate dal codice JavaScript lato client (`credentials` proprietà impostata su `include`).</span><span class="sxs-lookup"><span data-stu-id="eedfb-153">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="eedfb-154">Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="eedfb-154">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="eedfb-155">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="eedfb-155">Handle token request errors</span></span>

<span data-ttu-id="eedfb-156">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel provider di identità (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le proprie credenziali.</span><span class="sxs-lookup"><span data-stu-id="eedfb-156">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="eedfb-157">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="eedfb-157">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="eedfb-158">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="eedfb-158">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="eedfb-159">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="eedfb-159">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="eedfb-160">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="eedfb-160">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="eedfb-161">Questo scenario si verifica se un utente `https://login.microsoftonline.com` visita e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="eedfb-161">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="eedfb-162">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-162">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="eedfb-163">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="eedfb-163">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="eedfb-164">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="eedfb-164">They are part of the nature of SPAs.</span></span> <span data-ttu-id="eedfb-165">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-165">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="eedfb-166">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="eedfb-166">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="eedfb-167">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-167">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="eedfb-168">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-168">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="eedfb-169">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="eedfb-169">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="eedfb-170">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="eedfb-170">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="eedfb-171">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="eedfb-171">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="eedfb-172">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="eedfb-172">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="eedfb-173">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="eedfb-173">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="eedfb-174">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-174">Store the current page state in session storage.</span></span> <span data-ttu-id="eedfb-175">Durante `OnInitializeAsync`, controllare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="eedfb-175">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="eedfb-176">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="eedfb-176">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="eedfb-177">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="eedfb-177">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="eedfb-178">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="eedfb-178">The following example shows how to:</span></span>

* <span data-ttu-id="eedfb-179">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-179">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="eedfb-180">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="eedfb-180">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="eedfb-181">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="eedfb-181">Save app state before an authentication operation</span></span>

<span data-ttu-id="eedfb-182">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="eedfb-182">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="eedfb-183">Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-183">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="eedfb-184">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-184">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="eedfb-185">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-185">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a><span data-ttu-id="eedfb-186">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="eedfb-186">Customize app routes</span></span>

<span data-ttu-id="eedfb-187">Per impostazione predefinita, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la libreria usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="eedfb-187">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="eedfb-188">Route</span><span class="sxs-lookup"><span data-stu-id="eedfb-188">Route</span></span>                            | <span data-ttu-id="eedfb-189">Scopo</span><span class="sxs-lookup"><span data-stu-id="eedfb-189">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="eedfb-190">Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-190">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="eedfb-191">Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-191">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="eedfb-192">Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="eedfb-192">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="eedfb-193">Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-193">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="eedfb-194">Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-194">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="eedfb-195">Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="eedfb-195">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="eedfb-196">Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-196">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="eedfb-197">Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-197">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="eedfb-198">Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-198">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="eedfb-199">Le route visualizzate nella tabella precedente sono configurabili tramite `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span><span class="sxs-lookup"><span data-stu-id="eedfb-199">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="eedfb-200">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-200">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="eedfb-201">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security`.</span><span class="sxs-lookup"><span data-stu-id="eedfb-201">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="eedfb-202">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="eedfb-203">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-203">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="eedfb-204">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il `RemoteAuthenticatorView` rendering di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="eedfb-204">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="eedfb-205">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="eedfb-205">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="eedfb-206">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="eedfb-206">Customize the authentication user interface</span></span>

<span data-ttu-id="eedfb-207">`RemoteAuthenticatorView`include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-207">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="eedfb-208">Ogni stato può essere personalizzato passando un oggetto personalizzato `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="eedfb-208">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="eedfb-209">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare `RemoteAuthenticatorView` il come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="eedfb-209">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="eedfb-210">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="eedfb-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="eedfb-211">`RemoteAuthenticatorView` Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-211">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="eedfb-212">Route</span><span class="sxs-lookup"><span data-stu-id="eedfb-212">Route</span></span>                            | <span data-ttu-id="eedfb-213">Frammento</span><span class="sxs-lookup"><span data-stu-id="eedfb-213">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="eedfb-214">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="eedfb-214">Customize the user</span></span>

<span data-ttu-id="eedfb-215">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="eedfb-215">Users bound to the app can be customized.</span></span> <span data-ttu-id="eedfb-216">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-216">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="eedfb-217">Creare una classe che estende la `RemoteUserAccount` classe:</span><span class="sxs-lookup"><span data-stu-id="eedfb-217">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="eedfb-218">Creare una factory che estende `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-218">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="eedfb-219">Registrare `CustomAccountFactory` per il provider di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="eedfb-219">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="eedfb-220">Una delle seguenti registrazioni è valida:</span><span class="sxs-lookup"><span data-stu-id="eedfb-220">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="eedfb-221">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-221">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="eedfb-222">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-222">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="eedfb-223">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-223">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="eedfb-224">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="eedfb-224">Support prerendering with authentication</span></span>

<span data-ttu-id="eedfb-225">Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="eedfb-225">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="eedfb-226">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-226">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="eedfb-227">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-227">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="eedfb-228">Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices`:</span><span class="sxs-lookup"><span data-stu-id="eedfb-228">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient(new HttpClient 
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

<span data-ttu-id="eedfb-229">Nell'app `Startup.ConfigureServices`server registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="eedfb-229">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="eedfb-230">Nel `Startup.Configure` metodo dell'app server sostituire `endpoints.MapFallbackToFile("index.html")` con: `endpoints.MapFallbackToPage("/_Host")`</span><span class="sxs-lookup"><span data-stu-id="eedfb-230">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="eedfb-231">Nell'app Server creare una cartella *pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="eedfb-231">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="eedfb-232">Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="eedfb-232">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="eedfb-233">Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eedfb-233">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="eedfb-234">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="eedfb-234">Update the file's contents:</span></span>

* <span data-ttu-id="eedfb-235">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="eedfb-235">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="eedfb-236">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="eedfb-236">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="eedfb-237">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="eedfb-237">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="eedfb-238">Quando si esegue l'autenticazione e l'autorizzazione Blazor di un'app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="eedfb-238">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="eedfb-239">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="eedfb-239">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="eedfb-240">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="eedfb-240">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="eedfb-241">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="eedfb-241">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="eedfb-242">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="eedfb-242">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="eedfb-243">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="eedfb-243">In this scenario:</span></span>

* <span data-ttu-id="eedfb-244">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="eedfb-244">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="eedfb-245">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="eedfb-245">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="eedfb-246">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="eedfb-246">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="eedfb-247">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="eedfb-247">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="eedfb-248">Configurare Identity con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="eedfb-248">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="eedfb-249">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="eedfb-249">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="eedfb-250">Quando un utente esegue l'accesso Identity , raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="eedfb-250">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="eedfb-251">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="eedfb-251">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="eedfb-252">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="eedfb-252">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="eedfb-253">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="eedfb-253">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="eedfb-254">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di Identity terze parti direttamente da nel client.</span><span class="sxs-lookup"><span data-stu-id="eedfb-254">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="eedfb-255">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="eedfb-255">We don't recommend this approach.</span></span> <span data-ttu-id="eedfb-256">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="eedfb-256">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="eedfb-257">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="eedfb-257">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="eedfb-258">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="eedfb-258">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="eedfb-259">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="eedfb-259">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="eedfb-260">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="eedfb-260">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="eedfb-261">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="eedfb-261">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="eedfb-262">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="eedfb-262">Make an API call from the client to the server API.</span></span> <span data-ttu-id="eedfb-263">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="eedfb-263">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="eedfb-264">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="eedfb-264">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="eedfb-265">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="eedfb-265">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="eedfb-266">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="eedfb-266">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
