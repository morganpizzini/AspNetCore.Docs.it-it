---
<span data-ttu-id="3390f-101">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-103">'Blazor'</span></span>
- <span data-ttu-id="3390f-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-104">'Identity'</span></span>
- <span data-ttu-id="3390f-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-106">'Razor'</span></span>
- <span data-ttu-id="3390f-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="3390f-108">BlazorScenari di sicurezza aggiuntivi ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="3390f-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="3390f-109">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3390f-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="3390f-110">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="3390f-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="3390f-111">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio può essere usato con <xref:System.Net.Http.HttpClient> per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="3390f-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="3390f-112">I token vengono acquisiti utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> servizio esistente.</span><span class="sxs-lookup"><span data-stu-id="3390f-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="3390f-113">Se non è possibile acquisire un token, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="3390f-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="3390f-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>dispone di un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="3390f-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="3390f-115">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="3390f-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="3390f-116">Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="3390f-117">Per praticità, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="3390f-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="3390f-118">I Blazor modelli webassembly abilitati per l'autenticazione ora usano <xref:System.Net.Http.IHttpClientFactory> nel progetto API server per configurare un <xref:System.Net.Http.HttpClient> con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="3390f-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="3390f-119">Se il client viene creato con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> nell'esempio precedente, <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="3390f-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="3390f-120">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene quindi usato per effettuare richieste autorizzate usando un semplice modello [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="3390f-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="3390f-121">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3390f-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="3390f-122">HttpClient tipizzato</span><span class="sxs-lookup"><span data-stu-id="3390f-122">Typed HttpClient</span></span>

<span data-ttu-id="3390f-123">È possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe.</span><span class="sxs-lookup"><span data-stu-id="3390f-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="3390f-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="3390f-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="3390f-125">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="3390f-126">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3390f-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="3390f-127">Configurare il gestore HttpClient</span><span class="sxs-lookup"><span data-stu-id="3390f-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="3390f-128">Il gestore può essere configurato ulteriormente con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="3390f-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="3390f-129">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="3390f-130">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="3390f-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="3390f-131">Se l' Blazor app webassembly USA in genere un valore predefinito sicuro <xref:System.Net.Http.HttpClient> , l'app può anche creare richieste API Web non autenticate o non autorizzate configurando un oggetto denominato <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="3390f-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="3390f-132">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="3390f-133">La registrazione precedente è aggiunta alla registrazione predefinita protetta esistente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="3390f-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="3390f-134">Un componente crea <xref:System.Net.Http.HttpClient> da <xref:System.Net.Http.IHttpClientFactory> per effettuare richieste non autenticate o non autorizzate:</span><span class="sxs-lookup"><span data-stu-id="3390f-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="3390f-135">Il controller nell'API del server, `WeatherForecastNoAuthenticationController` per l'esempio precedente, non è contrassegnato con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="3390f-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="3390f-136">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="3390f-136">Request additional access tokens</span></span>

<span data-ttu-id="3390f-137">I token di accesso possono essere ottenuti manualmente chiamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="3390f-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="3390f-138">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="3390f-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="3390f-139">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app client.</span><span class="sxs-lookup"><span data-stu-id="3390f-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="3390f-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="3390f-141">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti.</span><span class="sxs-lookup"><span data-stu-id="3390f-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="3390f-142">In un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="3390f-142">In a Razor component:</span></span>

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

<span data-ttu-id="3390f-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Restituisce</span><span class="sxs-lookup"><span data-stu-id="3390f-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="3390f-144">`true`con l'oggetto `token` da usare.</span><span class="sxs-lookup"><span data-stu-id="3390f-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="3390f-145">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="3390f-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="3390f-146">HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch</span><span class="sxs-lookup"><span data-stu-id="3390f-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="3390f-147">Quando è in esecuzione su webassembly in un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> può essere usato per personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="3390f-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="3390f-148">Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta.</span><span class="sxs-lookup"><span data-stu-id="3390f-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="3390f-149">Il componente seguente effettua una `POST` richiesta a un endpoint API to do list nel server e Mostra il corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="3390f-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="3390f-150">L'implementazione di .NET webassembly <xref:System.Net.Http.HttpClient> Usa [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="3390f-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="3390f-151">Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="3390f-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="3390f-152">Le opzioni di richiesta di recupero HTTP possono essere configurate con <xref:System.Net.Http.HttpRequestMessage> i metodi di estensione illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3390f-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="3390f-153">Metodo di estensione</span><span class="sxs-lookup"><span data-stu-id="3390f-153">Extension method</span></span> | <span data-ttu-id="3390f-154">Recupera la proprietà della richiesta</span><span class="sxs-lookup"><span data-stu-id="3390f-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="3390f-155">credenziali</span><span class="sxs-lookup"><span data-stu-id="3390f-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="3390f-156">cache</span><span class="sxs-lookup"><span data-stu-id="3390f-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="3390f-157">mode</span><span class="sxs-lookup"><span data-stu-id="3390f-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="3390f-158">integrità</span><span class="sxs-lookup"><span data-stu-id="3390f-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="3390f-159">È possibile impostare opzioni aggiuntive usando il metodo di <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> estensione più generico.</span><span class="sxs-lookup"><span data-stu-id="3390f-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="3390f-160">La risposta HTTP viene in genere memorizzata nel buffer in un' Blazor app webassembly per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="3390f-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="3390f-161">Per abilitare il supporto per il flusso di risposta, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodo di estensione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="3390f-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="3390f-162">Per includere le credenziali in una richiesta tra più origini, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="3390f-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="3390f-163">Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="3390f-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="3390f-164">Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l' `Authorization` intestazione deve essere consentita dal criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="3390f-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="3390f-165">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="3390f-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="3390f-166">Origin della richiesta ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="3390f-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="3390f-167">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="3390f-167">Any method (verb).</span></span>
* <span data-ttu-id="3390f-168">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="3390f-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="3390f-169">Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header` ), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="3390f-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="3390f-170">Credenziali impostate dal codice JavaScript lato client ( `credentials` proprietà impostata su `include` ).</span><span class="sxs-lookup"><span data-stu-id="3390f-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="3390f-171">Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="3390f-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="3390f-172">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="3390f-172">Handle token request errors</span></span>

<span data-ttu-id="3390f-173">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel Identity provider (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le credenziali.</span><span class="sxs-lookup"><span data-stu-id="3390f-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="3390f-174">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="3390f-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="3390f-175">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="3390f-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="3390f-176">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="3390f-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="3390f-177">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="3390f-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="3390f-178">Questo scenario si verifica se un utente visita `https://login.microsoftonline.com` e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="3390f-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="3390f-179">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="3390f-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="3390f-180">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="3390f-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="3390f-181">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="3390f-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="3390f-182">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="3390f-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="3390f-183">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="3390f-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="3390f-184">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="3390f-185">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="3390f-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="3390f-186">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="3390f-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="3390f-187">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="3390f-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="3390f-188">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="3390f-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="3390f-189">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="3390f-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="3390f-190">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="3390f-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="3390f-191">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="3390f-191">Store the current page state in session storage.</span></span> <span data-ttu-id="3390f-192">Durante l' [evento del ciclo](xref:blazor/lifecycle#component-initialization-methods) di vita OnInitializedAsync ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verificare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="3390f-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="3390f-193">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="3390f-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="3390f-194">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="3390f-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="3390f-195">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="3390f-195">The following example shows how to:</span></span>

* <span data-ttu-id="3390f-196">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="3390f-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="3390f-197">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="3390f-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="3390f-198">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3390f-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="3390f-199">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="3390f-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="3390f-200">Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="3390f-201">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="3390f-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="3390f-202">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3390f-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="3390f-203">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="3390f-203">Customize app routes</span></span>

<span data-ttu-id="3390f-204">Per impostazione predefinita, la libreria [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="3390f-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="3390f-205">Route</span><span class="sxs-lookup"><span data-stu-id="3390f-205">Route</span></span>                            | <span data-ttu-id="3390f-206">Scopo</span><span class="sxs-lookup"><span data-stu-id="3390f-206">Purpose</span></span> |
| ---
<span data-ttu-id="3390f-207">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-208">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-209">'Blazor'</span></span>
- <span data-ttu-id="3390f-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-210">'Identity'</span></span>
- <span data-ttu-id="3390f-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-212">'Razor'</span></span>
- <span data-ttu-id="3390f-213">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-214">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-215">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-216">'Blazor'</span></span>
- <span data-ttu-id="3390f-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-217">'Identity'</span></span>
- <span data-ttu-id="3390f-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-219">'Razor'</span></span>
- <span data-ttu-id="3390f-220">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-221">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-222">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-223">'Blazor'</span></span>
- <span data-ttu-id="3390f-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-224">'Identity'</span></span>
- <span data-ttu-id="3390f-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-226">'Razor'</span></span>
- <span data-ttu-id="3390f-227">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-228">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-229">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-230">'Blazor'</span></span>
- <span data-ttu-id="3390f-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-231">'Identity'</span></span>
- <span data-ttu-id="3390f-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-233">'Razor'</span></span>
- <span data-ttu-id="3390f-234">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-235">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-236">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-237">'Blazor'</span></span>
- <span data-ttu-id="3390f-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-238">'Identity'</span></span>
- <span data-ttu-id="3390f-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-240">'Razor'</span></span>
- <span data-ttu-id="3390f-241">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-242">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-243">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-244">'Blazor'</span></span>
- <span data-ttu-id="3390f-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-245">'Identity'</span></span>
- <span data-ttu-id="3390f-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-247">'Razor'</span></span>
- <span data-ttu-id="3390f-248">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-249">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-250">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-251">'Blazor'</span></span>
- <span data-ttu-id="3390f-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-252">'Identity'</span></span>
- <span data-ttu-id="3390f-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-254">'Razor'</span></span>
- <span data-ttu-id="3390f-255">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-256">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-257">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-258">'Blazor'</span></span>
- <span data-ttu-id="3390f-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-259">'Identity'</span></span>
- <span data-ttu-id="3390f-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-261">'Razor'</span></span>
- <span data-ttu-id="3390f-262">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-263">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-264">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-265">'Blazor'</span></span>
- <span data-ttu-id="3390f-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-266">'Identity'</span></span>
- <span data-ttu-id="3390f-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-268">'Razor'</span></span>
- <span data-ttu-id="3390f-269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-270">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-271">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-272">'Blazor'</span></span>
- <span data-ttu-id="3390f-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-273">'Identity'</span></span>
- <span data-ttu-id="3390f-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-275">'Razor'</span></span>
- <span data-ttu-id="3390f-276">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-277">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-278">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-279">'Blazor'</span></span>
- <span data-ttu-id="3390f-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-280">'Identity'</span></span>
- <span data-ttu-id="3390f-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-282">'Razor'</span></span>
- <span data-ttu-id="3390f-283">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-284">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-285">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-286">'Blazor'</span></span>
- <span data-ttu-id="3390f-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-287">'Identity'</span></span>
- <span data-ttu-id="3390f-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-289">'Razor'</span></span>
- <span data-ttu-id="3390f-290">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-291">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-292">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-293">'Blazor'</span></span>
- <span data-ttu-id="3390f-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-294">'Identity'</span></span>
- <span data-ttu-id="3390f-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-296">'Razor'</span></span>
- <span data-ttu-id="3390f-297">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-298">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-299">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-300">'Blazor'</span></span>
- <span data-ttu-id="3390f-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-301">'Identity'</span></span>
- <span data-ttu-id="3390f-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-303">'Razor'</span></span>
- <span data-ttu-id="3390f-304">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-304">'SignalR' uid:</span></span> 

<span data-ttu-id="3390f-305">---------------- | titolo del---:' ASP.NET Core Blazor di sicurezza aggiuntiva di un Webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-306">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-307">'Blazor'</span></span>
- <span data-ttu-id="3390f-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-308">'Identity'</span></span>
- <span data-ttu-id="3390f-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-310">'Razor'</span></span>
- <span data-ttu-id="3390f-311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-311">'SignalR' uid:</span></span> 

<span data-ttu-id="3390f-312">---- | | `authentication/login`           | Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="3390f-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="3390f-313">| | `authentication/login-callback`  | Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="3390f-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="3390f-314">| | `authentication/login-failed`    | Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="3390f-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="3390f-315">| | `authentication/logout`          | Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="3390f-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="3390f-316">| | `authentication/logout-callback` | Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="3390f-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="3390f-317">| | `authentication/logout-failed`   | Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="3390f-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="3390f-318">| | `authentication/logged-out`      | Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="3390f-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="3390f-319">| | `authentication/profile`         | Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="3390f-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="3390f-320">| | `authentication/register`        | Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="3390f-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="3390f-321">Le route visualizzate nella tabella precedente sono configurabili tramite <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3390f-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3390f-322">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="3390f-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="3390f-323">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security` .</span><span class="sxs-lookup"><span data-stu-id="3390f-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="3390f-324">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3390f-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3390f-325">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="3390f-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="3390f-326">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="3390f-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="3390f-327">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="3390f-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="3390f-328">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="3390f-328">Customize the authentication user interface</span></span>

<span data-ttu-id="3390f-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="3390f-330">Ogni stato può essere personalizzato passando un oggetto personalizzato <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="3390f-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="3390f-331">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="3390f-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="3390f-332">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="3390f-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="3390f-333"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="3390f-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="3390f-334">Route</span><span class="sxs-lookup"><span data-stu-id="3390f-334">Route</span></span>                            | <span data-ttu-id="3390f-335">Frammento</span><span class="sxs-lookup"><span data-stu-id="3390f-335">Fragment</span></span>                |
| ---
<span data-ttu-id="3390f-336">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-337">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-338">'Blazor'</span></span>
- <span data-ttu-id="3390f-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-339">'Identity'</span></span>
- <span data-ttu-id="3390f-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-341">'Razor'</span></span>
- <span data-ttu-id="3390f-342">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-343">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-344">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-345">'Blazor'</span></span>
- <span data-ttu-id="3390f-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-346">'Identity'</span></span>
- <span data-ttu-id="3390f-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-348">'Razor'</span></span>
- <span data-ttu-id="3390f-349">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-350">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-351">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-352">'Blazor'</span></span>
- <span data-ttu-id="3390f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-353">'Identity'</span></span>
- <span data-ttu-id="3390f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-355">'Razor'</span></span>
- <span data-ttu-id="3390f-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-357">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-358">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-359">'Blazor'</span></span>
- <span data-ttu-id="3390f-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-360">'Identity'</span></span>
- <span data-ttu-id="3390f-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-362">'Razor'</span></span>
- <span data-ttu-id="3390f-363">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-364">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-365">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-366">'Blazor'</span></span>
- <span data-ttu-id="3390f-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-367">'Identity'</span></span>
- <span data-ttu-id="3390f-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-369">'Razor'</span></span>
- <span data-ttu-id="3390f-370">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-371">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-372">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-373">'Blazor'</span></span>
- <span data-ttu-id="3390f-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-374">'Identity'</span></span>
- <span data-ttu-id="3390f-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-376">'Razor'</span></span>
- <span data-ttu-id="3390f-377">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-378">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-379">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-380">'Blazor'</span></span>
- <span data-ttu-id="3390f-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-381">'Identity'</span></span>
- <span data-ttu-id="3390f-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-383">'Razor'</span></span>
- <span data-ttu-id="3390f-384">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-385">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-386">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-387">'Blazor'</span></span>
- <span data-ttu-id="3390f-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-388">'Identity'</span></span>
- <span data-ttu-id="3390f-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-390">'Razor'</span></span>
- <span data-ttu-id="3390f-391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-392">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-393">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-394">'Blazor'</span></span>
- <span data-ttu-id="3390f-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-395">'Identity'</span></span>
- <span data-ttu-id="3390f-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-397">'Razor'</span></span>
- <span data-ttu-id="3390f-398">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-399">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-400">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-401">'Blazor'</span></span>
- <span data-ttu-id="3390f-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-402">'Identity'</span></span>
- <span data-ttu-id="3390f-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-404">'Razor'</span></span>
- <span data-ttu-id="3390f-405">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-406">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-407">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-408">'Blazor'</span></span>
- <span data-ttu-id="3390f-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-409">'Identity'</span></span>
- <span data-ttu-id="3390f-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-411">'Razor'</span></span>
- <span data-ttu-id="3390f-412">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-413">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-414">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-415">'Blazor'</span></span>
- <span data-ttu-id="3390f-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-416">'Identity'</span></span>
- <span data-ttu-id="3390f-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-418">'Razor'</span></span>
- <span data-ttu-id="3390f-419">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-420">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-421">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-422">'Blazor'</span></span>
- <span data-ttu-id="3390f-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-423">'Identity'</span></span>
- <span data-ttu-id="3390f-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-425">'Razor'</span></span>
- <span data-ttu-id="3390f-426">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-427">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-428">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-429">'Blazor'</span></span>
- <span data-ttu-id="3390f-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-430">'Identity'</span></span>
- <span data-ttu-id="3390f-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-432">'Razor'</span></span>
- <span data-ttu-id="3390f-433">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-433">'SignalR' uid:</span></span> 

<span data-ttu-id="3390f-434">---------------- | titolo del---:' ASP.NET Core Blazor di sicurezza aggiuntiva di un Webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-435">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-436">'Blazor'</span></span>
- <span data-ttu-id="3390f-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-437">'Identity'</span></span>
- <span data-ttu-id="3390f-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-439">'Razor'</span></span>
- <span data-ttu-id="3390f-440">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-441">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-442">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-443">'Blazor'</span></span>
- <span data-ttu-id="3390f-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-444">'Identity'</span></span>
- <span data-ttu-id="3390f-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-446">'Razor'</span></span>
- <span data-ttu-id="3390f-447">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-448">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-449">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-450">'Blazor'</span></span>
- <span data-ttu-id="3390f-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-451">'Identity'</span></span>
- <span data-ttu-id="3390f-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-453">'Razor'</span></span>
- <span data-ttu-id="3390f-454">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-455">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-456">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-457">'Blazor'</span></span>
- <span data-ttu-id="3390f-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-458">'Identity'</span></span>
- <span data-ttu-id="3390f-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-460">'Razor'</span></span>
- <span data-ttu-id="3390f-461">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-462">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-463">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-464">'Blazor'</span></span>
- <span data-ttu-id="3390f-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-465">'Identity'</span></span>
- <span data-ttu-id="3390f-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-467">'Razor'</span></span>
- <span data-ttu-id="3390f-468">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-469">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-470">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-471">'Blazor'</span></span>
- <span data-ttu-id="3390f-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-472">'Identity'</span></span>
- <span data-ttu-id="3390f-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-474">'Razor'</span></span>
- <span data-ttu-id="3390f-475">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-476">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-477">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-478">'Blazor'</span></span>
- <span data-ttu-id="3390f-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-479">'Identity'</span></span>
- <span data-ttu-id="3390f-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-481">'Razor'</span></span>
- <span data-ttu-id="3390f-482">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-483">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-484">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-485">'Blazor'</span></span>
- <span data-ttu-id="3390f-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-486">'Identity'</span></span>
- <span data-ttu-id="3390f-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-488">'Razor'</span></span>
- <span data-ttu-id="3390f-489">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3390f-490">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' autore: Descrizione:' informazioni su come configurare il Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="3390f-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="3390f-491">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="3390f-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3390f-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3390f-492">'Blazor'</span></span>
- <span data-ttu-id="3390f-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3390f-493">'Identity'</span></span>
- <span data-ttu-id="3390f-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3390f-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="3390f-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3390f-495">'Razor'</span></span>
- <span data-ttu-id="3390f-496">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="3390f-496">'SignalR' uid:</span></span> 

<span data-ttu-id="3390f-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="3390f-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="3390f-498">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="3390f-498">Customize the user</span></span>

<span data-ttu-id="3390f-499">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="3390f-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="3390f-500">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3390f-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="3390f-501">Creare una classe che estende la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="3390f-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="3390f-502">Creare una factory che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="3390f-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="3390f-503">Registrare `CustomAccountFactory` per il provider di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="3390f-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="3390f-504">Una delle seguenti registrazioni è valida:</span><span class="sxs-lookup"><span data-stu-id="3390f-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="3390f-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3390f-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="3390f-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3390f-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="3390f-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="3390f-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="3390f-508">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="3390f-508">Support prerendering with authentication</span></span>

<span data-ttu-id="3390f-509">Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="3390f-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="3390f-510">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="3390f-511">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="3390f-512">Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="3390f-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="3390f-513">Nell'app Server `Startup.ConfigureServices` registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="3390f-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="3390f-514">Nel metodo dell'app Server `Startup.Configure` sostituire gli [endpoint. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) con [endpoint. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="3390f-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="3390f-515">Nell'app Server creare una cartella *pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="3390f-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="3390f-516">Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="3390f-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="3390f-517">Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3390f-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="3390f-518">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="3390f-518">Update the file's contents:</span></span>

* <span data-ttu-id="3390f-519">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="3390f-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="3390f-520">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="3390f-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="3390f-521">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="3390f-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="3390f-522">Quando si esegue l'autenticazione e l'autorizzazione di un' Blazor app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="3390f-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="3390f-523">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="3390f-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="3390f-524">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="3390f-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="3390f-525">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="3390f-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="3390f-526">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="3390f-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="3390f-527">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="3390f-527">In this scenario:</span></span>

* <span data-ttu-id="3390f-528">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="3390f-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="3390f-529">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="3390f-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="3390f-530">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="3390f-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="3390f-531">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="3390f-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="3390f-532">Configurare Identity con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="3390f-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="3390f-533">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="3390f-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="3390f-534">Quando un utente esegue Identity l'accesso, raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="3390f-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="3390f-535">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="3390f-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="3390f-536">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="3390f-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="3390f-537">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="3390f-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="3390f-538">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente da Identity nel client.</span><span class="sxs-lookup"><span data-stu-id="3390f-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="3390f-539">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="3390f-539">We don't recommend this approach.</span></span> <span data-ttu-id="3390f-540">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="3390f-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="3390f-541">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="3390f-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="3390f-542">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="3390f-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="3390f-543">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="3390f-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="3390f-544">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="3390f-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="3390f-545">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="3390f-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="3390f-546">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="3390f-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="3390f-547">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="3390f-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="3390f-548">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="3390f-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="3390f-549">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="3390f-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="3390f-550">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="3390f-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="3390f-551">Usa endpoint Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="3390f-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="3390f-552">I modelli e la libreria di autenticazione Blazor usano gli endpoint Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="3390f-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="3390f-553">Per usare un endpoint v 2.0, configurare l'opzione JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3390f-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="3390f-554">Nell'esempio seguente AAD è configurato per la versione 2.0 aggiungendo un `v2.0` segmento alla <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> proprietà:</span><span class="sxs-lookup"><span data-stu-id="3390f-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="3390f-555">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="3390f-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="3390f-556">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="3390f-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="3390f-557">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o nel file di impostazioni dell'app (*appSettings. JSON*) con la `Authority` chiave.</span><span class="sxs-lookup"><span data-stu-id="3390f-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="3390f-558">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="3390f-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="3390f-559">Per ulteriori informazioni, vedere la pagina relativa [all'aggiornamento alla piattaforma di identità Microsoft (v 2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="3390f-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
