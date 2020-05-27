---
<span data-ttu-id="1e3fc-101">Titolo:' ASP.NET Core Blazor Server scenari di sicurezza aggiuntivi ' autore: Descrizione:' informazioni su come configurare il Blazor Server per altri scenari di sicurezza .'</span><span class="sxs-lookup"><span data-stu-id="1e3fc-101">title: 'ASP.NET Core Blazor Server additional security scenarios' author: description: 'Learn how to configure Blazor Server for additional security scenarios.'</span></span>
<span data-ttu-id="1e3fc-102">monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1e3fc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1e3fc-103">'Blazor'</span></span>
- <span data-ttu-id="1e3fc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1e3fc-104">'Identity'</span></span>
- <span data-ttu-id="1e3fc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1e3fc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1e3fc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1e3fc-106">'Razor'</span></span>
- <span data-ttu-id="1e3fc-107">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="1e3fc-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="1e3fc-108">BlazorScenari di sicurezza aggiuntivi di ASP.NET Core Server</span><span class="sxs-lookup"><span data-stu-id="1e3fc-108">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="1e3fc-109">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1e3fc-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="1e3fc-110">Passare i token a un' Blazor app Server</span><span class="sxs-lookup"><span data-stu-id="1e3fc-110">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="1e3fc-111">I token disponibili all'esterno dei Razor componenti di un' Blazor app Server possono essere passati ai componenti di con l'approccio descritto in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-111">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="1e3fc-112">Per il codice di esempio, incluso un `Startup.ConfigureServices` esempio completo, vedere il [passaggio di token a un' Blazor applicazione lato server](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="1e3fc-112">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="1e3fc-113">Autenticare l' Blazor app Server come si farebbe con un' Razor applicazione MVC o pagine normali.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-113">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="1e3fc-114">Eseguire il provisioning e salvare i token nel cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-114">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="1e3fc-115">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-115">For example:</span></span>

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

<span data-ttu-id="1e3fc-116">Definire una classe per passare lo stato iniziale dell'app con i token di accesso e di aggiornamento:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-116">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1e3fc-117">Definire un servizio del provider di token con **ambito** che può essere usato nell' Blazor app per risolvere i token da [inserimento delle dipendenze](xref:blazor/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="1e3fc-117">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="1e3fc-118">In `Startup.ConfigureServices` aggiungere servizi per:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-118">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="1e3fc-119">Nel file *_Host. cshtml* creare un'istanza di e `InitialApplicationState` passarla come parametro all'app:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-119">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="1e3fc-120">Nel `App` componente (*app. Razor*) risolvere il servizio e inizializzarlo con i dati del parametro:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-120">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="1e3fc-121">Nel servizio che esegue una richiesta API protetta, inserire il provider di token e recuperare il token per chiamare l'API:</span><span class="sxs-lookup"><span data-stu-id="1e3fc-121">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="1e3fc-122">Usa endpoint Open ID Connect (OIDC) v 2.0</span><span class="sxs-lookup"><span data-stu-id="1e3fc-122">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="1e3fc-123">I modelli e la libreria di autenticazione Blazor usano gli endpoint Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-123">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="1e3fc-124">Per usare un endpoint v 2.0, configurare l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> opzione in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1e3fc-124">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="1e3fc-125">In alternativa, l'impostazione può essere eseguita nel file delle impostazioni dell'app (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="1e3fc-125">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="1e3fc-126">Se l'uso di un segmento nell'autorità non è appropriato per il provider OIDC dell'app, ad esempio con i provider non AAD, impostare <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direttamente la proprietà.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-126">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="1e3fc-127">Impostare la proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> o nel file di impostazioni dell'app con la <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> chiave.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-127">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="1e3fc-128">Modifiche al codice</span><span class="sxs-lookup"><span data-stu-id="1e3fc-128">Code changes</span></span>

* <span data-ttu-id="1e3fc-129">L'elenco di attestazioni nel token ID cambia per gli endpoint della versione 2.0.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-129">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="1e3fc-130">Per ulteriori informazioni, vedere [perché eseguire l'aggiornamento alla piattaforma di identità Microsoft (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="1e3fc-130">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="1e3fc-131">nella documentazione di Azure.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-131">in the Azure documentation.</span></span>
* <span data-ttu-id="1e3fc-132">Poiché le risorse vengono specificate negli URI di ambito per gli endpoint v 2.0, rimuovere l' <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> impostazione della proprietà in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> :</span><span class="sxs-lookup"><span data-stu-id="1e3fc-132">Since resources are specified in scope URIs for v2.0 endpoints, remove the the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *App ID URI*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

*appsettings.json*:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="1e3fc-133">È possibile trovare l'URI ID app da usare nella descrizione della registrazione dell'app provider OIDC.</span><span class="sxs-lookup"><span data-stu-id="1e3fc-133">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
