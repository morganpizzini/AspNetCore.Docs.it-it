---
title: BlazorScenari di sicurezza aggiuntivi ASP.NET Core Webassembly
author: guardrex
description: Informazioni su come configurare Blazor webassembly per altri scenari di sicurezza.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153482"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Scenari di sicurezza aggiuntivi del webassembly ASP.NET Core Blazer

Di [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a>Connetti token alle richieste in uscita

Il `AuthorizationMessageHandler` servizio può essere usato con `HttpClient` per associare token di accesso alle richieste in uscita. I token vengono acquisiti utilizzando il `IAccessTokenProvider` servizio esistente. Se non è possibile acquisire un token, `AccessTokenNotAvailableException` viene generata un'eccezione. `AccessTokenNotAvailableException`dispone di un `Redirect` metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token. Il `AuthorizationMessageHandler` può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati utilizzando il `ConfigureHandler` metodo.

Nell'esempio seguente `AuthorizationMessageHandler` configura un `HttpClient` in `Program.Main` (*Program.cs*):

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

Per praticità, `BaseAddressAuthorizationMessageHandler` è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato. I modelli webassembly di Blazer abilitati per l'autenticazione ora usano <xref:System.Net.Http.IHttpClientFactory> nel progetto API server per configurare un <xref:System.Net.Http.HttpClient> con `BaseAddressAuthorizationMessageHandler` :

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

Se il client viene creato con `CreateClient` nell'esempio precedente, <xref:System.Net.Http.HttpClient> vengono fornite le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.

L'oggetto configurato <xref:System.Net.Http.HttpClient> viene quindi usato per effettuare richieste autorizzate usando un `try-catch` modello semplice.

`FetchData`componente (*pages/fetchData. Razor*):

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

## <a name="typed-httpclient"></a>HttpClient tipizzato

È possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe.

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

`FetchData`componente (*pages/fetchData. Razor*):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Configurare il gestore HttpClient

Il gestore può essere configurato ulteriormente con <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> per le richieste HTTP in uscita.

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Richieste API Web non autenticate o non autorizzate in un'app con un client predefinito sicuro

Se l'app webassembly Blazer USA in genere un valore predefinito sicuro <xref:System.Net.Http.HttpClient> , l'app può anche eseguire richieste API Web non autenticate o non autorizzate configurando un oggetto denominato <xref:System.Net.Http.HttpClient> :

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

La registrazione precedente è aggiunta alla registrazione predefinita protetta esistente <xref:System.Net.Http.HttpClient> .

Un componente crea <xref:System.Net.Http.HttpClient> da <xref:System.Net.Http.IHttpClientFactory> per effettuare richieste non autenticate o non autorizzate:

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
> Il controller nell'API del server, `WeatherForecastNoAuthenticationController` per l'esempio precedente, non è contrassegnato con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo.

## <a name="request-additional-access-tokens"></a>Richiedere token di accesso aggiuntivi

I token di accesso possono essere ottenuti manualmente chiamando `IAccessTokenProvider.RequestAccessToken` .

Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica. Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app client.

`Program.Main`(*Program.cs*):

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

Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti.

In un componente Razor:

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

`TryGetToken`Restituisce

* `true`con l'oggetto `token` da usare.
* `false`Se il token non viene recuperato.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient e HttpRequestMessage con le opzioni di richiesta dell'API fetch

Quando è in esecuzione su webassembly in un'app webassembly blazer, [HttpClient](xref:fundamentals/http-requests) e <xref:System.Net.Http.HttpRequestMessage> può essere usato per personalizzare le richieste. Ad esempio, è possibile specificare il metodo HTTP e le intestazioni di richiesta. Il componente seguente effettua una `POST` richiesta a un endpoint API to do list nel server e Mostra il corpo della risposta:

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

L'implementazione di .NET webassembly `HttpClient` Usa [WindowOrWorkerGlobalScope. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Il recupero consente la configurazione [di diverse opzioni specifiche della richiesta](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Le opzioni di richiesta di recupero HTTP possono essere configurate con `HttpRequestMessage` i metodi di estensione illustrati nella tabella seguente.

| `HttpRequestMessage`Metodo di estensione | Recupera la proprietà della richiesta |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [credenziali](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [integrità](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

È possibile impostare opzioni aggiuntive usando il metodo di `SetBrowserRequestOption` estensione più generico.
 
La risposta HTTP viene in genere memorizzata nel buffer in un'app webassembly blazer per abilitare il supporto per le letture della sincronizzazione sul contenuto della risposta. Per abilitare il supporto per il flusso di risposta, usare il `SetBrowserResponseStreamingEnabled` metodo di estensione nella richiesta.

Per includere le credenziali in una richiesta tra più origini, usare il `SetBrowserRequestCredentials` metodo di estensione:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Per altre informazioni sulle opzioni di recupero delle API, vedere la pagina relativa alla [documentazione Web MDN: WindowOrWorkerGlobalScope. fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Quando si inviano le credenziali (cookie/intestazioni di autorizzazione) nelle richieste CORS, l' `Authorization` intestazione deve essere consentita dal criterio CORS.

Il criterio seguente include la configurazione per:

* Origin della richiesta ( `http://localhost:5000` , `https://localhost:5001` ).
* Qualsiasi metodo (verbo).
* `Content-Type`e `Authorization` intestazioni. Per consentire un'intestazione personalizzata (ad esempio, `x-custom-header` ), elencare l'intestazione quando si chiama <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Credenziali impostate dal codice JavaScript lato client ( `credentials` proprietà impostata su `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Per ulteriori informazioni, vedere <xref:security/cors> e il componente tester richiesta HTTP dell'app di esempio (*Components/HTTPRequestTester. Razor*).

## <a name="handle-token-request-errors"></a>Gestione degli errori di richiesta di token

Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel provider di identità (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le proprie credenziali.

I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token. In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi. Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.

In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP. Questo scenario si verifica se un utente visita `https://login.microsoftonline.com` e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi. Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.

Questi scenari non sono specifici dell'autenticazione basata su token. Sono parte della natura delle Spa. Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.

Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:

* Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.
* Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.

Quando l'app richiede un token, esistono due possibili risultati:

* La richiesta ha esito positivo e l'app ha un token valido.
* La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.

Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento. Esistono diversi approcci con livelli di complessità crescenti:

* Archiviare lo stato della pagina corrente nell'archiviazione della sessione. Durante `OnInitializeAsync` , controllare se è possibile ripristinare lo stato prima di continuare.
* Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.
* Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.

L'esempio seguente illustra come:

* Mantenere lo stato prima di reindirizzare alla pagina di accesso.
* Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Salva lo stato dell'app prima di un'operazione di autenticazione

Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP. Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione. È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.

`Authentication`componente (*pages/Authentication. Razor*):

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

## <a name="customize-app-routes"></a>Personalizzare le route delle app

Per impostazione predefinita, la `Microsoft.AspNetCore.Components.WebAssembly.Authentication` libreria usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.

| Route                            | Scopo |
| -------------------------------- | ------- |
| `authentication/login`           | Attiva un'operazione di accesso. |
| `authentication/login-callback`  | Gestisce il risultato di qualsiasi operazione di accesso. |
| `authentication/login-failed`    | Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo. |
| `authentication/logout`          | Attiva un'operazione di disconnessione. |
| `authentication/logout-callback` | Gestisce il risultato di un'operazione di disconnessione. |
| `authentication/logout-failed`   | Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo. |
| `authentication/logged-out`      | Indica che l'utente ha eseguito correttamente la disconnessione. |
| `authentication/profile`         | Attiva un'operazione per modificare il profilo utente. |
| `authentication/register`        | Attiva un'operazione per registrare un nuovo utente. |

Le route visualizzate nella tabella precedente sono configurabili tramite `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` . Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.

Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security` .

`Authentication`componente (*pages/Authentication. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

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

Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering `RemoteAuthenticatorView` di con un parametro di azione esplicito:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.

## <a name="customize-the-authentication-user-interface"></a>Personalizzare l'interfaccia utente di autenticazione

`RemoteAuthenticatorView`include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione. Ogni stato può essere personalizzato passando un oggetto personalizzato `RenderFragment` . Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare il `RemoteAuthenticatorView` come indicato di seguito.

`Authentication`componente (*pages/Authentication. Razor*):

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

`RemoteAuthenticatorView`Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.

| Route                            | Frammento                |
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

## <a name="customize-the-user"></a>Personalizzare l'utente

Gli utenti associati all'app possono essere personalizzati. Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.

Creare una classe che estende la `RemoteUserAccount` classe:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Creare una factory che estende `AccountClaimsPrincipalFactory<TAccount>` :

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

Registrare `CustomAccountFactory` per il provider di autenticazione in uso. Una delle seguenti registrazioni è valida: 

* `AddOidcAuthentication`:

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

* `AddMsalAuthentication`:

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
  
* `AddApiAuthorization`:

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

## <a name="support-prerendering-with-authentication"></a>Supporto del prerendering con autenticazione

Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:

* Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.
* Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.

Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices` :

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

Nell'app Server `Startup.ConfigureServices` registrare i servizi aggiuntivi seguenti:

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

Nel metodo dell'app Server `Startup.Configure` sostituire `endpoints.MapFallbackToFile("index.html")` con `endpoints.MapFallbackToPage("/_Host")` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Nell'app Server creare una cartella *pages* se non esiste. Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server. Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* . Aggiornare il contenuto del file:

* Aggiungere `@page "_Host"` all'inizio del file.
* Sostituire il `<app>Loading...</app>` tag con il codice seguente:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opzioni per le app ospitate e i provider di accesso di terze parti

Quando si esegue l'autenticazione e l'autorizzazione di un' Blazor app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente. Quello scelto dipende dallo scenario.

Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticare gli utenti per chiamare solo le API di terze parti protette

Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 In questo scenario:

* Il server che ospita l'app non svolge un ruolo.
* Le API nel server non possono essere protette.
* L'app può chiamare solo API di terze parti protette.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte

Configurare Identity con un provider di accesso di terze parti. Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.

Quando un utente esegue Identity l'accesso, raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione. A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Usare un token di accesso al server per recuperare il token di accesso di terze parti

Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server. Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente da Identity nel client.

Questo approccio non è consigliato. Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico. In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato. Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.

* Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.
* Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Eseguire chiamate API dal client all'API server per chiamare le API di terze parti

Eseguire una chiamata API dal client all'API del server. Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.

Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:

* Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.
* L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.
