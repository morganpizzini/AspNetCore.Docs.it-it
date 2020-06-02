---
<span data-ttu-id="b2201-101">title:' ASP.NET Core gli Blazor scenari di sicurezza aggiuntiva di webassembly ' Author: guardrex Description:' informazioni su come configurare Blazor webassembly per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="b2201-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: guardrex description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="b2201-102">monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 06/01/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="b2201-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/01/2020 no-loc:</span></span>
- <span data-ttu-id="b2201-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b2201-103">'Blazor'</span></span>
- <span data-ttu-id="b2201-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b2201-104">'Identity'</span></span>
- <span data-ttu-id="b2201-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b2201-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b2201-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b2201-106">'Razor'</span></span>
- <span data-ttu-id="b2201-107">' SignalR ' UID: Security/Blazer/webassembly/scenari aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="b2201-107">'SignalR' uid: security/blazor/webassembly/additional-scenarios</span></span>

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="b2201-108">BlazorScenari di sicurezza aggiuntivi ASP.NET Core Webassembly</span><span class="sxs-lookup"><span data-stu-id="b2201-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="b2201-109">Di [Javier Calvarro Nelson](https://github.com/javiercn) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b2201-109">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="b2201-110">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="b2201-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="b2201-111">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> servizio può essere usato con <xref:System.Net.Http.HttpClient> per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="b2201-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="b2201-112">I token vengono acquisiti utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> servizio esistente.</span><span class="sxs-lookup"><span data-stu-id="b2201-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="b2201-113">Se non è possibile acquisire un token, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="b2201-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="b2201-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>dispone di un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="b2201-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="b2201-115">Il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati utilizzando il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> metodo.</span><span class="sxs-lookup"><span data-stu-id="b2201-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="b2201-116">Usare uno degli approcci seguenti per configurare un gestore di messaggi per le richieste in uscita:</span><span class="sxs-lookup"><span data-stu-id="b2201-116">Use either of the following approaches to configure a message handler for outgoing requests:</span></span>

* <span data-ttu-id="b2201-117">[Classe AuthorizationMessageHandler personalizzata](#custom-authorizationmessagehandler-class) (*scelta consigliata*)</span><span class="sxs-lookup"><span data-stu-id="b2201-117">[Custom AuthorizationMessageHandler class](#custom-authorizationmessagehandler-class) (*Recommended*)</span></span>
* [<span data-ttu-id="b2201-118">Configurare AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b2201-118">Configure AuthorizationMessageHandler</span></span>](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="b2201-119">Classe AuthorizationMessageHandler personalizzata</span><span class="sxs-lookup"><span data-stu-id="b2201-119">Custom AuthorizationMessageHandler class</span></span>

<span data-ttu-id="b2201-120">Nell'esempio seguente viene estesa una classe personalizzata <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> che può essere usata per configurare un oggetto <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="b2201-120">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> that can be used to configure an <xref:System.Net.Http.HttpClient>:</span></span>

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

<span data-ttu-id="b2201-121">In `Program.Main` (*Program.cs*), un oggetto <xref:System.Net.Http.HttpClient> viene configurato con il gestore dei messaggi di autorizzazione personalizzati:</span><span class="sxs-lookup"><span data-stu-id="b2201-121">In `Program.Main` (*Program.cs*), an <xref:System.Net.Http.HttpClient> is configured with the custom authorization message handler:</span></span>

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="b2201-122">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene usato per effettuare richieste autorizzate usando il modello [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="b2201-122">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="b2201-123">Se il client viene creato con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (pacchetto[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ), <xref:System.Net.Http.HttpClient> vengono fornite istanze che includono token di accesso quando si effettuano richieste all'API del server:</span><span class="sxs-lookup"><span data-stu-id="b2201-123">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API:</span></span>

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

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="b2201-124">Configurare AuthorizationMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b2201-124">Configure AuthorizationMessageHandler</span></span>

<span data-ttu-id="b2201-125">Nell'esempio seguente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configura un <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-125">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddTransient(sp =>
{
    return new HttpClient(sp.GetRequiredService<AuthorizationMessageHandler>()
        .ConfigureHandler(
            authorizedUrls: new [] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" }))
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        };
});
```

<span data-ttu-id="b2201-126">Per praticità, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="b2201-126">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="b2201-127">I Blazor modelli webassembly abilitati per l'autenticazione usano ora <xref:System.Net.Http.IHttpClientFactory> (pacchetto[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) nel progetto API server per configurare un <xref:System.Net.Http.HttpClient> con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="b2201-127">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="b2201-128">Se il client viene creato con <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> nell'esempio precedente, <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="b2201-128">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="b2201-129">L'oggetto configurato <xref:System.Net.Http.HttpClient> viene usato per effettuare richieste autorizzate usando il modello [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :</span><span class="sxs-lookup"><span data-stu-id="b2201-129">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="b2201-130">HttpClient tipizzato</span><span class="sxs-lookup"><span data-stu-id="b2201-130">Typed HttpClient</span></span>

<span data-ttu-id="b2201-131">È possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe.</span><span class="sxs-lookup"><span data-stu-id="b2201-131">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="b2201-132">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="b2201-132">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="b2201-133">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-133">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="b2201-134">`FetchData`componente (*pages/fetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b2201-134">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="b2201-135">Configurare il gestore HttpClient</span><span class="sxs-lookup"><span data-stu-id="b2201-135">Configure the HttpClient handler</span></span>

<span data-ttu-id="b2201-136">Il gestore può essere configurato ulteriormente con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> per le richieste HTTP in uscita.</span><span class="sxs-lookup"><span data-stu-id="b2201-136">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="b2201-137">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-137">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="b2201-138">Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro</span><span class="sxs-lookup"><span data-stu-id="b2201-138">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="b2201-139">Se l' Blazor app webassembly USA in genere un valore predefinito sicuro <xref:System.Net.Http.HttpClient> , l'app può anche creare richieste API Web non autenticate o non autorizzate configurando un oggetto denominato <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="b2201-139">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="b2201-140">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-140">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="b2201-141">La registrazione precedente è aggiunta alla registrazione predefinita protetta esistente <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="b2201-141">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="b2201-142">Un componente crea <xref:System.Net.Http.HttpClient> dal <xref:System.Net.Http.IHttpClientFactory> (pacchetto[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) per eseguire richieste non autenticate o non autorizzate:</span><span class="sxs-lookup"><span data-stu-id="b2201-142">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) package) to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="b2201-143">Il controller nell'API del server, `WeatherForecastNoAuthenticationController` per l'esempio precedente, non è contrassegnato con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo.</span><span class="sxs-lookup"><span data-stu-id="b2201-143">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="b2201-144">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="b2201-144">Request additional access tokens</span></span>

<span data-ttu-id="b2201-145">I token di accesso possono essere ottenuti manualmente chiamando `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="b2201-145">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="b2201-146">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="b2201-146">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="b2201-147">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app client.</span><span class="sxs-lookup"><span data-stu-id="b2201-147">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="b2201-148">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-148">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b2201-149">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti.</span><span class="sxs-lookup"><span data-stu-id="b2201-149">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="b2201-150">In un Razor componente:</span><span class="sxs-lookup"><span data-stu-id="b2201-150">In a Razor component:</span></span>

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

<span data-ttu-id="b2201-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>Restituisce</span><span class="sxs-lookup"><span data-stu-id="b2201-151"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="b2201-152">`true`con l'oggetto `token` da usare.</span><span class="sxs-lookup"><span data-stu-id="b2201-152">`true` with the `token` for use.</span></span>
* <span data-ttu-id="b2201-153">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="b2201-153">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="b2201-154">HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch</span><span class="sxs-lookup"><span data-stu-id="b2201-154">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="b2201-155">Quando è in esecuzione su webassembly in un' Blazor app webassembly, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> può essere usato per personalizzare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b2201-155">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="b2201-156">Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta.</span><span class="sxs-lookup"><span data-stu-id="b2201-156">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="b2201-157">Il componente seguente effettua una `POST` richiesta a un endpoint API to do list nel server e Mostra il corpo della risposta:</span><span class="sxs-lookup"><span data-stu-id="b2201-157">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="b2201-158">L'implementazione di .NET webassembly <xref:System.Net.Http.HttpClient> Usa [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="b2201-158">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="b2201-159">Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b2201-159">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="b2201-160">Le opzioni di richiesta di recupero HTTP possono essere configurate con <xref:System.Net.Http.HttpRequestMessage> i metodi di estensione illustrati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="b2201-160">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="b2201-161">Metodo di estensione</span><span class="sxs-lookup"><span data-stu-id="b2201-161">Extension method</span></span> | <span data-ttu-id="b2201-162">Recupera la proprietà della richiesta</span><span class="sxs-lookup"><span data-stu-id="b2201-162">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="b2201-163">credenziali</span><span class="sxs-lookup"><span data-stu-id="b2201-163">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="b2201-164">cache</span><span class="sxs-lookup"><span data-stu-id="b2201-164">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="b2201-165">mode</span><span class="sxs-lookup"><span data-stu-id="b2201-165">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="b2201-166">integrità</span><span class="sxs-lookup"><span data-stu-id="b2201-166">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="b2201-167">È possibile impostare opzioni aggiuntive usando il metodo di <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> estensione più generico.</span><span class="sxs-lookup"><span data-stu-id="b2201-167">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="b2201-168">La risposta HTTP viene in genere memorizzata nel buffer in un' Blazor app webassembly per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta.</span><span class="sxs-lookup"><span data-stu-id="b2201-168">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="b2201-169">Per abilitare il supporto per il flusso di risposta, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> metodo di estensione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="b2201-169">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="b2201-170">Per includere le credenziali in una richiesta tra più origini, usare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b2201-170">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="b2201-171">Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="b2201-171">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="b2201-172">Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l' `Authorization` intestazione deve essere consentita dal criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="b2201-172">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="b2201-173">Il criterio seguente include la configurazione per:</span><span class="sxs-lookup"><span data-stu-id="b2201-173">The following policy includes configuration for:</span></span>

* <span data-ttu-id="b2201-174">Origin della richiesta ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="b2201-174">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="b2201-175">Qualsiasi metodo (verbo).</span><span class="sxs-lookup"><span data-stu-id="b2201-175">Any method (verb).</span></span>
* <span data-ttu-id="b2201-176">`Content-Type`e `Authorization` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="b2201-176">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="b2201-177">Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header` ), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="b2201-177">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="b2201-178">Credenziali impostate dal codice JavaScript lato client ( `credentials` proprietà impostata su `include` ).</span><span class="sxs-lookup"><span data-stu-id="b2201-178">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="b2201-179">Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b2201-179">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="b2201-180">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="b2201-180">Handle token request errors</span></span>

<span data-ttu-id="b2201-181">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel Identity provider (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le credenziali.</span><span class="sxs-lookup"><span data-stu-id="b2201-181">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="b2201-182">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="b2201-182">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="b2201-183">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="b2201-183">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="b2201-184">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="b2201-184">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="b2201-185">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="b2201-185">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="b2201-186">Questo scenario si verifica se un utente visita `https://login.microsoftonline.com` e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="b2201-186">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="b2201-187">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="b2201-187">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="b2201-188">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="b2201-188">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="b2201-189">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="b2201-189">They are part of the nature of SPAs.</span></span> <span data-ttu-id="b2201-190">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="b2201-190">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="b2201-191">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="b2201-191">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="b2201-192">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-192">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="b2201-193">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="b2201-193">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="b2201-194">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="b2201-194">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="b2201-195">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="b2201-195">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="b2201-196">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="b2201-196">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="b2201-197">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="b2201-197">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="b2201-198">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="b2201-198">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="b2201-199">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="b2201-199">Store the current page state in session storage.</span></span> <span data-ttu-id="b2201-200">Durante l' [evento del ciclo](xref:blazor/lifecycle#component-initialization-methods) di vita OnInitializedAsync ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ), verificare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="b2201-200">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="b2201-201">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="b2201-201">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="b2201-202">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="b2201-202">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="b2201-203">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="b2201-203">The following example shows how to:</span></span>

* <span data-ttu-id="b2201-204">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="b2201-204">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="b2201-205">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="b2201-205">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="b2201-206">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b2201-206">Save app state before an authentication operation</span></span>

<span data-ttu-id="b2201-207">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="b2201-207">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="b2201-208">Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-208">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="b2201-209">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="b2201-209">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="b2201-210">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b2201-210">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="b2201-211">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="b2201-211">Customize app routes</span></span>

<span data-ttu-id="b2201-212">Per impostazione predefinita, la libreria [Microsoft. AspNetCore. Components. webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="b2201-212">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="b2201-213">Route</span><span class="sxs-lookup"><span data-stu-id="b2201-213">Route</span></span>                            | <span data-ttu-id="b2201-214">Scopo</span><span class="sxs-lookup"><span data-stu-id="b2201-214">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="b2201-215">Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="b2201-215">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="b2201-216">Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="b2201-216">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="b2201-217">Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="b2201-217">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="b2201-218">Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="b2201-218">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="b2201-219">Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="b2201-219">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="b2201-220">Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="b2201-220">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="b2201-221">Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="b2201-221">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="b2201-222">Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="b2201-222">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="b2201-223">Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="b2201-223">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="b2201-224">Le route visualizzate nella tabella precedente sono configurabili tramite <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b2201-224">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="b2201-225">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="b2201-225">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="b2201-226">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security` .</span><span class="sxs-lookup"><span data-stu-id="b2201-226">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="b2201-227">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b2201-227">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="b2201-228">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="b2201-228">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="b2201-229">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="b2201-229">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="b2201-230">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="b2201-230">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="b2201-231">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="b2201-231">Customize the authentication user interface</span></span>

<span data-ttu-id="b2201-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-232"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="b2201-233">Ogni stato può essere personalizzato passando un oggetto personalizzato <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="b2201-233">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="b2201-234">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare il <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="b2201-234">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="b2201-235">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b2201-235">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="b2201-236"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="b2201-236">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="b2201-237">Route</span><span class="sxs-lookup"><span data-stu-id="b2201-237">Route</span></span>                            | <span data-ttu-id="b2201-238">Frammento</span><span class="sxs-lookup"><span data-stu-id="b2201-238">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="b2201-239">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="b2201-239">Customize the user</span></span>

<span data-ttu-id="b2201-240">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="b2201-240">Users bound to the app can be customized.</span></span> <span data-ttu-id="b2201-241">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b2201-241">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="b2201-242">Creare una classe che estende la <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> classe:</span><span class="sxs-lookup"><span data-stu-id="b2201-242">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="b2201-243">Creare una factory che estende <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="b2201-243">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="b2201-244">Registrare `CustomAccountFactory` per il provider di autenticazione in uso.</span><span class="sxs-lookup"><span data-stu-id="b2201-244">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="b2201-245">Una delle seguenti registrazioni è valida:</span><span class="sxs-lookup"><span data-stu-id="b2201-245">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="b2201-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="b2201-246"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="b2201-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="b2201-247"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="b2201-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="b2201-248"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="b2201-249">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="b2201-249">Support prerendering with authentication</span></span>

<span data-ttu-id="b2201-250">Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="b2201-250">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="b2201-251">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-251">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="b2201-252">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-252">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="b2201-253">Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices` :</span><span class="sxs-lookup"><span data-stu-id="b2201-253">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="b2201-254">Nell'app Server `Startup.ConfigureServices` registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="b2201-254">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="b2201-255">Nel metodo dell'app Server `Startup.Configure` sostituire gli [endpoint. MapFallbackToFile ("index. html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) con [endpoint. MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="b2201-255">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="b2201-256">Nell'app Server creare una cartella *pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="b2201-256">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="b2201-257">Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="b2201-257">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="b2201-258">Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="b2201-258">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="b2201-259">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="b2201-259">Update the file's contents:</span></span>

* <span data-ttu-id="b2201-260">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="b2201-260">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="b2201-261">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="b2201-261">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="b2201-262">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="b2201-262">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="b2201-263">Quando si esegue l'autenticazione e l'autorizzazione di un' Blazor app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="b2201-263">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="b2201-264">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="b2201-264">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="b2201-265">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="b2201-265">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="b2201-266">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="b2201-266">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="b2201-267">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="b2201-267">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="b2201-268">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="b2201-268">In this scenario:</span></span>

* <span data-ttu-id="b2201-269">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="b2201-269">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="b2201-270">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="b2201-270">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="b2201-271">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="b2201-271">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="b2201-272">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="b2201-272">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="b2201-273">Configurare Identity con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="b2201-273">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="b2201-274">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="b2201-274">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="b2201-275">Quando un utente esegue Identity l'accesso, raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="b2201-275">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="b2201-276">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="b2201-276">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="b2201-277">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="b2201-277">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="b2201-278">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="b2201-278">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="b2201-279">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente da Identity nel client.</span><span class="sxs-lookup"><span data-stu-id="b2201-279">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="b2201-280">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="b2201-280">We don't recommend this approach.</span></span> <span data-ttu-id="b2201-281">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="b2201-281">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="b2201-282">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="b2201-282">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="b2201-283">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="b2201-283">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="b2201-284">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="b2201-284">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="b2201-285">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="b2201-285">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="b2201-286">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="b2201-286">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="b2201-287">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="b2201-287">Make an API call from the client to the server API.</span></span> <span data-ttu-id="b2201-288">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="b2201-288">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="b2201-289">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="b2201-289">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="b2201-290">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="b2201-290">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="b2201-291">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="b2201-291">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="b2201-292">Usa endpoint Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="b2201-292">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="b2201-293">I modelli e la libreria di autenticazione Blazor usano gli endpoint Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="b2201-293">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="b2201-294">Per usare un endpoint v 2.0, configurare l'opzione JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="b2201-294">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="b2201-295">Nell'esempio seguente AAD è configurato per la versione 2.0 aggiungendo un `v2.0` segmento alla <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> proprietà:</span><span class="sxs-lookup"><span data-stu-id="b2201-295">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="b2201-296">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="b2201-296">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="b2201-297">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="b2201-297">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="b2201-298">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> o nel file di impostazioni dell'app (*appSettings. JSON*) con la `Authority` chiave.</span><span class="sxs-lookup"><span data-stu-id="b2201-298">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="b2201-299">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="b2201-299">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="b2201-300">Per ulteriori informazioni, vedere la pagina relativa [all'aggiornamento alla piattaforma di identità Microsoft (v 2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="b2201-300">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
