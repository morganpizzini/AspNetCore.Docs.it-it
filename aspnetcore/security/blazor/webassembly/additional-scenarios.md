---
<span data-ttu-id="6382e-101">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-103">'Blazor'</span></span>
- <span data-ttu-id="6382e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-104">'Identity'</span></span>
- <span data-ttu-id="6382e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-106">'Razor'</span></span>
- <span data-ttu-id="6382e-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="6382e-108">BlazorScenari di sicurezza aggiuntivi ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="6382e-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="6382e-109">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="6382e-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="6382e-110">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="6382e-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="6382e-111">Il `AuthorizationMessageHandler` servizio può essere usato con `HttpClient` per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="6382e-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="6382e-112">I token vengono acquisiti utilizzando il `IAccessTokenProvider` servizio esistente.</span><span class="sxs-lookup"><span data-stu-id="6382e-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="6382e-113">Se non è possibile acquisire un token, `AccessTokenNotAvailableException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="6382e-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="6382e-114">`AccessTokenNotAvailableException`dispone di un `Redirect` metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="6382e-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="6382e-115">Il `AuthorizationMessageHandler` può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati utilizzando il `ConfigureHandler` metodo.</span><span class="sxs-lookup"><span data-stu-id="6382e-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="6382e-116">Nell'esempio seguente `AuthorizationMessageHandler` configura un `HttpClient` in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6382e-117">Per praticità, `BaseAddressAuthorizationMessageHandler` è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="6382e-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="6382e-118">I Blazor modelli webassembly abilitati per l'autenticazione ora usano <xref:System.Net.Http.IHttpClientFactory> nel progetto API server per configurare un <xref:System.Net.Http.HttpClient> con `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="6382e-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="6382e-119">Se il client viene creato con `CreateClient` nell'esempio precedente, <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="6382e-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="6382e-120">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene quindi usato per effettuare richieste autorizzate usando un `try-catch` modello semplice.</span><span class="sxs-lookup"><span data-stu-id="6382e-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="6382e-121">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6382e-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="6382e-122">HttpClient tipizzato</span><span class="sxs-lookup"><span data-stu-id="6382e-122">Typed HttpClient</span></span>

<span data-ttu-id="6382e-123">È possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe.</span><span class="sxs-lookup"><span data-stu-id="6382e-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="6382e-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="6382e-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="6382e-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="6382e-126">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6382e-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="6382e-127">Configurare il gestore HttpClient</span><span class="sxs-lookup"><span data-stu-id="6382e-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="6382e-128">Il gestore può essere configurato ulteriormente con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="6382e-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="6382e-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="6382e-130">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="6382e-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="6382e-131">Se l' Blazor app webassembly USA in genere un valore predefinito sicuro <xref:System.Net.Http.HttpClient> , l'app può anche creare richieste API Web non autenticate o non autorizzate configurando un oggetto denominato <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="6382e-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="6382e-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="6382e-133">La registrazione precedente è aggiunta alla registrazione predefinita protetta esistente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="6382e-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="6382e-134">Un componente crea <xref:System.Net.Http.HttpClient> da <xref:System.Net.Http.IHttpClientFactory> per effettuare richieste non autenticate o non autorizzate:</span><span class="sxs-lookup"><span data-stu-id="6382e-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="6382e-135">Il controller nell'API del server, `WeatherForecastNoAuthenticationController` per l'esempio precedente, non è contrassegnato con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="6382e-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="6382e-136">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="6382e-136">Request additional access tokens</span></span>

<span data-ttu-id="6382e-137">I token di accesso possono essere ottenuti manualmente chiamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="6382e-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="6382e-138">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="6382e-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="6382e-139">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app client.</span><span class="sxs-lookup"><span data-stu-id="6382e-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="6382e-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6382e-141">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti.</span><span class="sxs-lookup"><span data-stu-id="6382e-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="6382e-142">In un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="6382e-142">In a Razor component:</span></span>

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

<span data-ttu-id="6382e-143">`TryGetToken`Restituisce</span><span class="sxs-lookup"><span data-stu-id="6382e-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="6382e-144">`true`con l'oggetto `token` da usare.</span><span class="sxs-lookup"><span data-stu-id="6382e-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="6382e-145">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="6382e-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="6382e-146">HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch</span><span class="sxs-lookup"><span data-stu-id="6382e-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="6382e-147">Quando è in esecuzione su webassembly in un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> può essere usato per personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="6382e-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="6382e-148">Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta.</span><span class="sxs-lookup"><span data-stu-id="6382e-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="6382e-149">Il componente seguente effettua una `POST` richiesta a un endpoint API to do list nel server e Mostra il corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="6382e-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="6382e-150">L'implementazione di .NET webassembly `HttpClient` Usa [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="6382e-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="6382e-151">Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="6382e-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="6382e-152">Le opzioni di richiesta di recupero HTTP possono essere configurate con `HttpRequestMessage` i metodi di estensione illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="6382e-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="6382e-153">`HttpRequestMessage`Metodo di estensione</span><span class="sxs-lookup"><span data-stu-id="6382e-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="6382e-154">Recupera la proprietà della richiesta</span><span class="sxs-lookup"><span data-stu-id="6382e-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="6382e-155">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-156">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-157">'Blazor'</span></span>
- <span data-ttu-id="6382e-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-158">'Identity'</span></span>
- <span data-ttu-id="6382e-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-160">'Razor'</span></span>
- <span data-ttu-id="6382e-161">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-162">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-163">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-164">'Blazor'</span></span>
- <span data-ttu-id="6382e-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-165">'Identity'</span></span>
- <span data-ttu-id="6382e-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-167">'Razor'</span></span>
- <span data-ttu-id="6382e-168">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-169">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-170">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-171">'Blazor'</span></span>
- <span data-ttu-id="6382e-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-172">'Identity'</span></span>
- <span data-ttu-id="6382e-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-174">'Razor'</span></span>
- <span data-ttu-id="6382e-175">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-176">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-177">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-178">'Blazor'</span></span>
- <span data-ttu-id="6382e-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-179">'Identity'</span></span>
- <span data-ttu-id="6382e-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-181">'Razor'</span></span>
- <span data-ttu-id="6382e-182">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-183">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-184">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-185">'Blazor'</span></span>
- <span data-ttu-id="6382e-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-186">'Identity'</span></span>
- <span data-ttu-id="6382e-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-188">'Razor'</span></span>
- <span data-ttu-id="6382e-189">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-190">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-191">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-192">'Blazor'</span></span>
- <span data-ttu-id="6382e-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-193">'Identity'</span></span>
- <span data-ttu-id="6382e-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-195">'Razor'</span></span>
- <span data-ttu-id="6382e-196">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-197">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-198">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-199">'Blazor'</span></span>
- <span data-ttu-id="6382e-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-200">'Identity'</span></span>
- <span data-ttu-id="6382e-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-202">'Razor'</span></span>
- <span data-ttu-id="6382e-203">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-204">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-205">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-206">'Blazor'</span></span>
- <span data-ttu-id="6382e-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-207">'Identity'</span></span>
- <span data-ttu-id="6382e-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-209">'Razor'</span></span>
- <span data-ttu-id="6382e-210">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-211">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-212">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-213">'Blazor'</span></span>
- <span data-ttu-id="6382e-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-214">'Identity'</span></span>
- <span data-ttu-id="6382e-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-216">'Razor'</span></span>
- <span data-ttu-id="6382e-217">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-218">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-219">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-220">'Blazor'</span></span>
- <span data-ttu-id="6382e-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-221">'Identity'</span></span>
- <span data-ttu-id="6382e-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-223">'Razor'</span></span>
- <span data-ttu-id="6382e-224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-225">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-226">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-227">'Blazor'</span></span>
- <span data-ttu-id="6382e-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-228">'Identity'</span></span>
- <span data-ttu-id="6382e-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-230">'Razor'</span></span>
- <span data-ttu-id="6382e-231">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-232">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-233">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-234">'Blazor'</span></span>
- <span data-ttu-id="6382e-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-235">'Identity'</span></span>
- <span data-ttu-id="6382e-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-237">'Razor'</span></span>
- <span data-ttu-id="6382e-238">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-239">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-240">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-241">'Blazor'</span></span>
- <span data-ttu-id="6382e-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-242">'Identity'</span></span>
- <span data-ttu-id="6382e-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-244">'Razor'</span></span>
- <span data-ttu-id="6382e-245">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-246">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-247">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-248">'Blazor'</span></span>
- <span data-ttu-id="6382e-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-249">'Identity'</span></span>
- <span data-ttu-id="6382e-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-251">'Razor'</span></span>
- <span data-ttu-id="6382e-252">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-253">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-254">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-255">'Blazor'</span></span>
- <span data-ttu-id="6382e-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-256">'Identity'</span></span>
- <span data-ttu-id="6382e-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-258">'Razor'</span></span>
- <span data-ttu-id="6382e-259">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-260">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-261">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-262">'Blazor'</span></span>
- <span data-ttu-id="6382e-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-263">'Identity'</span></span>
- <span data-ttu-id="6382e-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-265">'Razor'</span></span>
- <span data-ttu-id="6382e-266">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-266">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-267">------------------- | titolo del---:' ASP.NET Core Blazor di sicurezza aggiuntiva di un Webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-268">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-269">'Blazor'</span></span>
- <span data-ttu-id="6382e-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-270">'Identity'</span></span>
- <span data-ttu-id="6382e-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-272">'Razor'</span></span>
- <span data-ttu-id="6382e-273">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-274">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-275">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-276">'Blazor'</span></span>
- <span data-ttu-id="6382e-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-277">'Identity'</span></span>
- <span data-ttu-id="6382e-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-279">'Razor'</span></span>
- <span data-ttu-id="6382e-280">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-281">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-282">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-283">'Blazor'</span></span>
- <span data-ttu-id="6382e-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-284">'Identity'</span></span>
- <span data-ttu-id="6382e-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-286">'Razor'</span></span>
- <span data-ttu-id="6382e-287">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-288">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-289">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-290">'Blazor'</span></span>
- <span data-ttu-id="6382e-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-291">'Identity'</span></span>
- <span data-ttu-id="6382e-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-293">'Razor'</span></span>
- <span data-ttu-id="6382e-294">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-295">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-296">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-297">'Blazor'</span></span>
- <span data-ttu-id="6382e-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-298">'Identity'</span></span>
- <span data-ttu-id="6382e-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-300">'Razor'</span></span>
- <span data-ttu-id="6382e-301">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-302">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-303">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-304">'Blazor'</span></span>
- <span data-ttu-id="6382e-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-305">'Identity'</span></span>
- <span data-ttu-id="6382e-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-307">'Razor'</span></span>
- <span data-ttu-id="6382e-308">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-309">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-310">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-311">'Blazor'</span></span>
- <span data-ttu-id="6382e-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-312">'Identity'</span></span>
- <span data-ttu-id="6382e-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-314">'Razor'</span></span>
- <span data-ttu-id="6382e-315">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-316">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-317">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-318">'Blazor'</span></span>
- <span data-ttu-id="6382e-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-319">'Identity'</span></span>
- <span data-ttu-id="6382e-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-321">'Razor'</span></span>
- <span data-ttu-id="6382e-322">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-323">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-324">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-325">'Blazor'</span></span>
- <span data-ttu-id="6382e-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-326">'Identity'</span></span>
- <span data-ttu-id="6382e-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-328">'Razor'</span></span>
- <span data-ttu-id="6382e-329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-329">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-330">----------- | | `SetBrowserRequestCredentials`         |  [credenziali](https://developer.mozilla.org/docs/Web/API/Request/credentials) | `SetBrowserRequestCache` |               |  [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [modalità](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [integrità](https://developer.mozilla.org/docs/Web/API/Request/integrity) di |</span><span class="sxs-lookup"><span data-stu-id="6382e-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="6382e-331">È possibile impostare opzioni aggiuntive usando il metodo di `SetBrowserRequestOption` estensione più generico.</span><span class="sxs-lookup"><span data-stu-id="6382e-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="6382e-332">La risposta HTTP viene in genere memorizzata nel buffer in un' Blazor app webassembly per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="6382e-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="6382e-333">Per abilitare il supporto per il flusso di risposta, usare il `SetBrowserResponseStreamingEnabled` metodo di estensione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="6382e-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="6382e-334">Per includere le credenziali in una richiesta tra più origini, usare il `SetBrowserRequestCredentials` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="6382e-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="6382e-335">Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="6382e-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="6382e-336">Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l' `Authorization` intestazione deve essere consentita dal criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="6382e-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="6382e-337">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="6382e-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="6382e-338">Origin della richiesta ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="6382e-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="6382e-339">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="6382e-339">Any method (verb).</span></span>
* <span data-ttu-id="6382e-340">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="6382e-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="6382e-341">Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header` ), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="6382e-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="6382e-342">Credenziali impostate dal codice JavaScript lato client ( `credentials` proprietà impostata su `include` ).</span><span class="sxs-lookup"><span data-stu-id="6382e-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="6382e-343">Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="6382e-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="6382e-344">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="6382e-344">Handle token request errors</span></span>

<span data-ttu-id="6382e-345">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel Identity provider (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le credenziali.</span><span class="sxs-lookup"><span data-stu-id="6382e-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="6382e-346">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="6382e-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="6382e-347">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="6382e-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="6382e-348">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="6382e-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="6382e-349">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="6382e-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="6382e-350">Questo scenario si verifica se un utente visita `https://login.microsoftonline.com` e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="6382e-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="6382e-351">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="6382e-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="6382e-352">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="6382e-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="6382e-353">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="6382e-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="6382e-354">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="6382e-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="6382e-355">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6382e-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="6382e-356">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="6382e-357">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="6382e-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="6382e-358">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="6382e-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="6382e-359">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="6382e-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="6382e-360">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="6382e-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="6382e-361">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="6382e-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="6382e-362">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="6382e-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="6382e-363">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="6382e-363">Store the current page state in session storage.</span></span> <span data-ttu-id="6382e-364">Durante `OnInitializeAsync` , controllare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="6382e-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="6382e-365">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="6382e-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="6382e-366">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="6382e-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="6382e-367">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="6382e-367">The following example shows how to:</span></span>

* <span data-ttu-id="6382e-368">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="6382e-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="6382e-369">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="6382e-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="6382e-370">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6382e-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="6382e-371">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="6382e-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="6382e-372">Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="6382e-373">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="6382e-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="6382e-374">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6382e-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="6382e-375">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="6382e-375">Customize app routes</span></span>

<span data-ttu-id="6382e-376">Per impostazione predefinita, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` libreria usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="6382e-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="6382e-377">Route</span><span class="sxs-lookup"><span data-stu-id="6382e-377">Route</span></span>                            | <span data-ttu-id="6382e-378">Scopo</span><span class="sxs-lookup"><span data-stu-id="6382e-378">Purpose</span></span> |
| ---
<span data-ttu-id="6382e-379">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-380">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-381">'Blazor'</span></span>
- <span data-ttu-id="6382e-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-382">'Identity'</span></span>
- <span data-ttu-id="6382e-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-384">'Razor'</span></span>
- <span data-ttu-id="6382e-385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-386">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-387">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-388">'Blazor'</span></span>
- <span data-ttu-id="6382e-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-389">'Identity'</span></span>
- <span data-ttu-id="6382e-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-391">'Razor'</span></span>
- <span data-ttu-id="6382e-392">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-393">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-394">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-395">'Blazor'</span></span>
- <span data-ttu-id="6382e-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-396">'Identity'</span></span>
- <span data-ttu-id="6382e-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-398">'Razor'</span></span>
- <span data-ttu-id="6382e-399">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-400">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-401">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-402">'Blazor'</span></span>
- <span data-ttu-id="6382e-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-403">'Identity'</span></span>
- <span data-ttu-id="6382e-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-405">'Razor'</span></span>
- <span data-ttu-id="6382e-406">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-407">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-408">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-409">'Blazor'</span></span>
- <span data-ttu-id="6382e-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-410">'Identity'</span></span>
- <span data-ttu-id="6382e-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-412">'Razor'</span></span>
- <span data-ttu-id="6382e-413">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-414">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-415">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-416">'Blazor'</span></span>
- <span data-ttu-id="6382e-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-417">'Identity'</span></span>
- <span data-ttu-id="6382e-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-419">'Razor'</span></span>
- <span data-ttu-id="6382e-420">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-421">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-422">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-423">'Blazor'</span></span>
- <span data-ttu-id="6382e-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-424">'Identity'</span></span>
- <span data-ttu-id="6382e-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-426">'Razor'</span></span>
- <span data-ttu-id="6382e-427">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-428">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-429">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-430">'Blazor'</span></span>
- <span data-ttu-id="6382e-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-431">'Identity'</span></span>
- <span data-ttu-id="6382e-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-433">'Razor'</span></span>
- <span data-ttu-id="6382e-434">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-435">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-436">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-437">'Blazor'</span></span>
- <span data-ttu-id="6382e-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-438">'Identity'</span></span>
- <span data-ttu-id="6382e-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-440">'Razor'</span></span>
- <span data-ttu-id="6382e-441">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-442">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-443">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-444">'Blazor'</span></span>
- <span data-ttu-id="6382e-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-445">'Identity'</span></span>
- <span data-ttu-id="6382e-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-447">'Razor'</span></span>
- <span data-ttu-id="6382e-448">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-449">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-450">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-451">'Blazor'</span></span>
- <span data-ttu-id="6382e-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-452">'Identity'</span></span>
- <span data-ttu-id="6382e-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-454">'Razor'</span></span>
- <span data-ttu-id="6382e-455">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-456">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-457">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-458">'Blazor'</span></span>
- <span data-ttu-id="6382e-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-459">'Identity'</span></span>
- <span data-ttu-id="6382e-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-461">'Razor'</span></span>
- <span data-ttu-id="6382e-462">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-463">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-464">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-465">'Blazor'</span></span>
- <span data-ttu-id="6382e-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-466">'Identity'</span></span>
- <span data-ttu-id="6382e-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-468">'Razor'</span></span>
- <span data-ttu-id="6382e-469">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-470">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-471">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-472">'Blazor'</span></span>
- <span data-ttu-id="6382e-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-473">'Identity'</span></span>
- <span data-ttu-id="6382e-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-475">'Razor'</span></span>
- <span data-ttu-id="6382e-476">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-476">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-477">---------------- | titolo del---:' ASP.NET Core Blazor di sicurezza aggiuntiva di un Webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-478">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-479">'Blazor'</span></span>
- <span data-ttu-id="6382e-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-480">'Identity'</span></span>
- <span data-ttu-id="6382e-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-482">'Razor'</span></span>
- <span data-ttu-id="6382e-483">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-483">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-484">---- | | `authentication/login`           | Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="6382e-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="6382e-485">| | `authentication/login-callback`  | Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="6382e-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="6382e-486">| | `authentication/login-failed`    | Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="6382e-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="6382e-487">| | `authentication/logout`          | Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="6382e-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="6382e-488">| | `authentication/logout-callback` | Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="6382e-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="6382e-489">| | `authentication/logout-failed`   | Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="6382e-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="6382e-490">| | `authentication/logged-out`      | Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="6382e-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="6382e-491">| | `authentication/profile`         | Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="6382e-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="6382e-492">| | `authentication/register`        | Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="6382e-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="6382e-493">Le route visualizzate nella tabella precedente sono configurabili tramite `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="6382e-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="6382e-494">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="6382e-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="6382e-495">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security` .</span><span class="sxs-lookup"><span data-stu-id="6382e-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="6382e-496">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6382e-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="6382e-497">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="6382e-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6382e-498">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering `RemoteAuthenticatorView` di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="6382e-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="6382e-499">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="6382e-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="6382e-500">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="6382e-500">Customize the authentication user interface</span></span>

<span data-ttu-id="6382e-501">`RemoteAuthenticatorView`include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="6382e-502">Ogni stato può essere personalizzato passando un oggetto personalizzato `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="6382e-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="6382e-503">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare il `RemoteAuthenticatorView` come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="6382e-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="6382e-504">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="6382e-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="6382e-505">`RemoteAuthenticatorView`Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="6382e-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="6382e-506">Route</span><span class="sxs-lookup"><span data-stu-id="6382e-506">Route</span></span>                            | <span data-ttu-id="6382e-507">Frammento</span><span class="sxs-lookup"><span data-stu-id="6382e-507">Fragment</span></span>                |
| ---
<span data-ttu-id="6382e-508">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-509">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-510">'Blazor'</span></span>
- <span data-ttu-id="6382e-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-511">'Identity'</span></span>
- <span data-ttu-id="6382e-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-513">'Razor'</span></span>
- <span data-ttu-id="6382e-514">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-515">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-516">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-517">'Blazor'</span></span>
- <span data-ttu-id="6382e-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-518">'Identity'</span></span>
- <span data-ttu-id="6382e-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-520">'Razor'</span></span>
- <span data-ttu-id="6382e-521">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-522">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-523">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-524">'Blazor'</span></span>
- <span data-ttu-id="6382e-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-525">'Identity'</span></span>
- <span data-ttu-id="6382e-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-527">'Razor'</span></span>
- <span data-ttu-id="6382e-528">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-529">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-530">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-531">'Blazor'</span></span>
- <span data-ttu-id="6382e-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-532">'Identity'</span></span>
- <span data-ttu-id="6382e-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-534">'Razor'</span></span>
- <span data-ttu-id="6382e-535">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-536">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-537">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-538">'Blazor'</span></span>
- <span data-ttu-id="6382e-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-539">'Identity'</span></span>
- <span data-ttu-id="6382e-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-541">'Razor'</span></span>
- <span data-ttu-id="6382e-542">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-543">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-544">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-545">'Blazor'</span></span>
- <span data-ttu-id="6382e-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-546">'Identity'</span></span>
- <span data-ttu-id="6382e-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-548">'Razor'</span></span>
- <span data-ttu-id="6382e-549">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-550">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-551">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-552">'Blazor'</span></span>
- <span data-ttu-id="6382e-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-553">'Identity'</span></span>
- <span data-ttu-id="6382e-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-555">'Razor'</span></span>
- <span data-ttu-id="6382e-556">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-557">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-558">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-559">'Blazor'</span></span>
- <span data-ttu-id="6382e-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-560">'Identity'</span></span>
- <span data-ttu-id="6382e-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-562">'Razor'</span></span>
- <span data-ttu-id="6382e-563">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-564">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-565">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-566">'Blazor'</span></span>
- <span data-ttu-id="6382e-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-567">'Identity'</span></span>
- <span data-ttu-id="6382e-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-569">'Razor'</span></span>
- <span data-ttu-id="6382e-570">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-571">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-572">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-573">'Blazor'</span></span>
- <span data-ttu-id="6382e-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-574">'Identity'</span></span>
- <span data-ttu-id="6382e-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-576">'Razor'</span></span>
- <span data-ttu-id="6382e-577">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-578">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-579">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-580">'Blazor'</span></span>
- <span data-ttu-id="6382e-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-581">'Identity'</span></span>
- <span data-ttu-id="6382e-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-583">'Razor'</span></span>
- <span data-ttu-id="6382e-584">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-585">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-586">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-587">'Blazor'</span></span>
- <span data-ttu-id="6382e-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-588">'Identity'</span></span>
- <span data-ttu-id="6382e-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-590">'Razor'</span></span>
- <span data-ttu-id="6382e-591">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-592">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-593">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-594">'Blazor'</span></span>
- <span data-ttu-id="6382e-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-595">'Identity'</span></span>
- <span data-ttu-id="6382e-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-597">'Razor'</span></span>
- <span data-ttu-id="6382e-598">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-599">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-600">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-601">'Blazor'</span></span>
- <span data-ttu-id="6382e-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-602">'Identity'</span></span>
- <span data-ttu-id="6382e-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-604">'Razor'</span></span>
- <span data-ttu-id="6382e-605">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-605">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-606">---------------- | titolo del---:' ASP.NET Core Blazor di sicurezza aggiuntiva di un Webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-607">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-608">'Blazor'</span></span>
- <span data-ttu-id="6382e-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-609">'Identity'</span></span>
- <span data-ttu-id="6382e-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-611">'Razor'</span></span>
- <span data-ttu-id="6382e-612">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-613">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-614">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-615">'Blazor'</span></span>
- <span data-ttu-id="6382e-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-616">'Identity'</span></span>
- <span data-ttu-id="6382e-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-618">'Razor'</span></span>
- <span data-ttu-id="6382e-619">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-620">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-621">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-622">'Blazor'</span></span>
- <span data-ttu-id="6382e-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-623">'Identity'</span></span>
- <span data-ttu-id="6382e-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-625">'Razor'</span></span>
- <span data-ttu-id="6382e-626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-627">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-628">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-629">'Blazor'</span></span>
- <span data-ttu-id="6382e-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-630">'Identity'</span></span>
- <span data-ttu-id="6382e-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-632">'Razor'</span></span>
- <span data-ttu-id="6382e-633">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-634">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-635">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-636">'Blazor'</span></span>
- <span data-ttu-id="6382e-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-637">'Identity'</span></span>
- <span data-ttu-id="6382e-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-639">'Razor'</span></span>
- <span data-ttu-id="6382e-640">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-641">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-642">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-643">'Blazor'</span></span>
- <span data-ttu-id="6382e-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-644">'Identity'</span></span>
- <span data-ttu-id="6382e-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-646">'Razor'</span></span>
- <span data-ttu-id="6382e-647">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-648">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-649">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-650">'Blazor'</span></span>
- <span data-ttu-id="6382e-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-651">'Identity'</span></span>
- <span data-ttu-id="6382e-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-653">'Razor'</span></span>
- <span data-ttu-id="6382e-654">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-655">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-656">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-657">'Blazor'</span></span>
- <span data-ttu-id="6382e-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-658">'Identity'</span></span>
- <span data-ttu-id="6382e-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-660">'Razor'</span></span>
- <span data-ttu-id="6382e-661">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6382e-662">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="6382e-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="6382e-663">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6382e-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6382e-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6382e-664">'Blazor'</span></span>
- <span data-ttu-id="6382e-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6382e-665">'Identity'</span></span>
- <span data-ttu-id="6382e-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6382e-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="6382e-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6382e-667">'Razor'</span></span>
- <span data-ttu-id="6382e-668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="6382e-668">'SignalR' uid:</span></span> 

<span data-ttu-id="6382e-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="6382e-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="6382e-670">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="6382e-670">Customize the user</span></span>

<span data-ttu-id="6382e-671">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="6382e-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="6382e-672">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6382e-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="6382e-673">Creare una classe che estende la `RemoteUserAccount` classe:</span><span class="sxs-lookup"><span data-stu-id="6382e-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="6382e-674">Creare una factory che estende `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="6382e-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="6382e-675">Registrare `CustomAccountFactory` per il provider di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="6382e-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="6382e-676">Una delle seguenti registrazioni è valida:</span><span class="sxs-lookup"><span data-stu-id="6382e-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="6382e-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="6382e-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="6382e-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="6382e-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="6382e-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="6382e-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="6382e-680">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="6382e-680">Support prerendering with authentication</span></span>

<span data-ttu-id="6382e-681">Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="6382e-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="6382e-682">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="6382e-683">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="6382e-684">Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="6382e-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="6382e-685">Nell'app Server `Startup.ConfigureServices` registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="6382e-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="6382e-686">Nel metodo dell'app Server `Startup.Configure` sostituire `endpoints.MapFallbackToFile("index.html")` con `endpoints.MapFallbackToPage("/_Host")` :</span><span class="sxs-lookup"><span data-stu-id="6382e-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="6382e-687">Nell'app Server creare una cartella *pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="6382e-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="6382e-688">Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="6382e-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="6382e-689">Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="6382e-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="6382e-690">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="6382e-690">Update the file's contents:</span></span>

* <span data-ttu-id="6382e-691">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="6382e-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="6382e-692">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="6382e-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="6382e-693">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="6382e-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="6382e-694">Quando si esegue l'autenticazione e l'autorizzazione di un' Blazor app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="6382e-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="6382e-695">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="6382e-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="6382e-696">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="6382e-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="6382e-697">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="6382e-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="6382e-698">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="6382e-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="6382e-699">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="6382e-699">In this scenario:</span></span>

* <span data-ttu-id="6382e-700">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="6382e-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="6382e-701">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="6382e-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="6382e-702">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="6382e-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="6382e-703">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="6382e-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="6382e-704">Configurare Identity con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="6382e-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="6382e-705">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="6382e-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="6382e-706">Quando un utente esegue Identity l'accesso, raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="6382e-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="6382e-707">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="6382e-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="6382e-708">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="6382e-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="6382e-709">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="6382e-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="6382e-710">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente da Identity nel client.</span><span class="sxs-lookup"><span data-stu-id="6382e-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="6382e-711">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="6382e-711">We don't recommend this approach.</span></span> <span data-ttu-id="6382e-712">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="6382e-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="6382e-713">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="6382e-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="6382e-714">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="6382e-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="6382e-715">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="6382e-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="6382e-716">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="6382e-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="6382e-717">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="6382e-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="6382e-718">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="6382e-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="6382e-719">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="6382e-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="6382e-720">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="6382e-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="6382e-721">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="6382e-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="6382e-722">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="6382e-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="6382e-723">Usa endpoint Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="6382e-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="6382e-724">I modelli e la libreria di autenticazione Blazor usano gli endpoint Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="6382e-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="6382e-725">Per usare un endpoint v 2.0, configurare l'opzione JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="6382e-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="6382e-726">Nell'esempio seguente AAD è configurato per la versione 2.0 aggiungendo un `v2.0` segmento alla `Authority` proprietà:</span><span class="sxs-lookup"><span data-stu-id="6382e-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="6382e-727">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="6382e-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="6382e-728">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare `Authority` direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="6382e-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="6382e-729">Impostare la proprietà in `JwtBearerOptions` o nel file di impostazioni dell'app con la `Authority` chiave.</span><span class="sxs-lookup"><span data-stu-id="6382e-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="6382e-730">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="6382e-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="6382e-731">Per ulteriori informazioni, vedere la pagina relativa [all'aggiornamento alla piattaforma di identità Microsoft (v 2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="6382e-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
