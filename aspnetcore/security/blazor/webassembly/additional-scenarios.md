---
title: Scenari Blazor di sicurezza aggiuntivi ASP.NET Core webassembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: cd1433d5716b9b595270209fa874a8cb93fdf699
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138430"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="e7b9c-102">Scenari di sicurezza aggiuntivi del webassembly ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="e7b9c-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="e7b9c-103">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="e7b9c-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="e7b9c-104">Richiedere token di accesso aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="e7b9c-104">Request additional access tokens</span></span>

<span data-ttu-id="e7b9c-105">La maggior parte delle app richiede solo un token di accesso per interagire con le risorse protette che usano.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="e7b9c-106">In alcuni scenari, un'app potrebbe richiedere più di un token per interagire con due o più risorse.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="e7b9c-107">Nell'esempio seguente sono necessari ulteriori Azure Active Directory (AAD) Microsoft Graph ambito dell'API da parte di un'app per leggere i dati utente e inviare messaggi di posta elettronica.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="e7b9c-108">Dopo aver aggiunto le autorizzazioni Microsoft Graph API nel portale di Azure AAD, gli ambiti aggiuntivi sono configurati nell'app`Program.Main`client (, *Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="e7b9c-109">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di effettuare il provisioning di un token di accesso con un determinato set di ambiti, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes, as seen in the following example:</span></span>

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
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

<span data-ttu-id="e7b9c-110">`TryGetToken`Restituisce</span><span class="sxs-lookup"><span data-stu-id="e7b9c-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="e7b9c-111">`true`con l' `token` oggetto da usare.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="e7b9c-112">`false`Se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-112">`false` if the token isn't retrieved.</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="e7b9c-113">Connetti token alle richieste in uscita</span><span class="sxs-lookup"><span data-stu-id="e7b9c-113">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="e7b9c-114">Il `AuthorizationMessageHandler` servizio può essere usato con `HttpClient` per associare token di accesso alle richieste in uscita.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-114">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="e7b9c-115">I token vengono acquisiti utilizzando il servizio `IAccessTokenProvider` esistente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-115">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="e7b9c-116">Se non è possibile acquisire un token, `AccessTokenNotAvailableException` viene generata un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-116">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="e7b9c-117">`AccessTokenNotAvailableException`dispone di `Redirect` un metodo che può essere utilizzato per passare all'utente al provider di identità per acquisire un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-117">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="e7b9c-118">Il `AuthorizationMessageHandler` può essere configurato con gli URL, gli ambiti e l'URL di ritorno autorizzati `ConfigureHandler` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-118">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="e7b9c-119">Nell' `AuthorizationMessageHandler` esempio seguente configura `HttpClient` un in `Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-119">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddSingleton(sp =>
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

<span data-ttu-id="e7b9c-120">Per praticità, `BaseAddressAuthorizationMessageHandler` è incluso un è preconfigurato con l'indirizzo di base dell'app come URL autorizzato.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-120">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="e7b9c-121">I modelli webassembly di Blazer abilitati per l'autenticazione ora usano [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) per `HttpClient` configurare un `BaseAddressAuthorizationMessageHandler`con:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-121">The authentication-enabled Blazor WebAssembly templates now use [IHttpClientFactory](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) to set up an `HttpClient` with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

<span data-ttu-id="e7b9c-122">Se il client viene creato con `CreateClient` nell'esempio precedente, vengono fornite `HttpClient` le istanze di che includono i token di accesso quando si effettuano richieste al progetto server.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-122">Where the client is created with `CreateClient` in the preceding example, the `HttpClient` is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="e7b9c-123">L'oggetto `HttpClient` configurato viene quindi usato per effettuare richieste autorizzate usando `try-catch` un modello semplice.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-123">The configured `HttpClient` is then used to make authorized requests using a simple `try-catch` pattern.</span></span> <span data-ttu-id="e7b9c-124">Il componente `FetchData` seguente richiede i dati delle previsioni meteo:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-124">The following `FetchData` component requests weather forecast data:</span></span>

```csharp
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

<span data-ttu-id="e7b9c-125">In alternativa, è possibile definire un client tipizzato che gestisce tutti i problemi di acquisizione di token e HTTP all'interno di una singola classe:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-125">Alternatively, you can define a typed client that handles all of the HTTP and token acquisition concerns within a single class:</span></span>

<span data-ttu-id="e7b9c-126">*WeatherClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-126">*WeatherClient.cs*:</span></span>

```csharp
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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="e7b9c-127">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-127">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="e7b9c-128">*FetchData. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-128">*FetchData.razor*:</span></span>

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a><span data-ttu-id="e7b9c-129">Gestione degli errori di richiesta di token</span><span class="sxs-lookup"><span data-stu-id="e7b9c-129">Handle token request errors</span></span>

<span data-ttu-id="e7b9c-130">Quando un'applicazione a pagina singola (SPA) autentica un utente usando Open ID Connect (OIDC), lo stato di autenticazione viene gestito localmente all'interno della SPA e nel provider di identità (IP) sotto forma di cookie di sessione impostato come risultato dell'utente che fornisce le proprie credenziali.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-130">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="e7b9c-131">I token che l'IP emette per l'utente in genere sono validi per brevi periodi di tempo, circa un'ora in modo normale, quindi l'app client deve recuperare periodicamente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-131">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="e7b9c-132">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-132">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="e7b9c-133">Nella maggior parte dei casi, i client OIDC sono in grado di effettuare il provisioning di nuovi token senza richiedere all'utente di eseguire di nuovo l'autenticazione grazie allo stato di autenticazione o alla "sessione" mantenuta all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-133">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="e7b9c-134">In alcuni casi il client non è in grado di ottenere un token senza interazione dell'utente, ad esempio quando per qualche motivo l'utente si disconnette esplicitamente dall'IP.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-134">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="e7b9c-135">Questo scenario si verifica se un utente `https://login.microsoftonline.com` visita e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Tutti i token che il client include potrebbero non essere più validi.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-135">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="e7b9c-136">Inoltre, il client non è in grado di effettuare il provisioning di un nuovo token senza interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-136">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="e7b9c-137">Questi scenari non sono specifici dell'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-137">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="e7b9c-138">Sono parte della natura delle Spa.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-138">They are part of the nature of SPAs.</span></span> <span data-ttu-id="e7b9c-139">Una SPA che usa i cookie non riesce anche a chiamare un'API server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-139">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="e7b9c-140">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-140">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="e7b9c-141">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, l'utente potrebbe essere necessario eseguire di nuovo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-141">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="e7b9c-142">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-142">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="e7b9c-143">Quando l'app richiede un token, esistono due possibili risultati:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-143">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="e7b9c-144">La richiesta ha esito positivo e l'app ha un token valido.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-144">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="e7b9c-145">La richiesta ha esito negativo e l'app deve autenticare di nuovo l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-145">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="e7b9c-146">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-146">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="e7b9c-147">Esistono diversi approcci con livelli di complessità crescenti:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-147">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="e7b9c-148">Archiviare lo stato della pagina corrente nell'archiviazione della sessione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-148">Store the current page state in session storage.</span></span> <span data-ttu-id="e7b9c-149">Durante `OnInitializeAsync`, controllare se è possibile ripristinare lo stato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-149">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="e7b9c-150">Aggiungere un parametro della stringa di query e usarlo come metodo per segnalare all'app che è necessario riattivare lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-150">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="e7b9c-151">Aggiungere un parametro della stringa di query con un identificatore univoco per archiviare i dati nell'archiviazione della sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-151">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="e7b9c-152">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-152">The following example shows how to:</span></span>

* <span data-ttu-id="e7b9c-153">Mantenere lo stato prima di reindirizzare alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-153">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="e7b9c-154">Ripristinare lo stato precedente in seguito all'autenticazione utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-154">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="e7b9c-155">Salva lo stato dell'app prima di un'operazione di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e7b9c-155">Save app state before an authentication operation</span></span>

<span data-ttu-id="e7b9c-156">Durante un'operazione di autenticazione, esistono casi in cui si vuole salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-156">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="e7b9c-157">Questo può succedere quando si usa un elemento come un contenitore di stato e si vuole ripristinare lo stato dopo l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-157">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="e7b9c-158">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare lo stato dopo che l'operazione di autenticazione è stata completata correttamente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-158">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="e7b9c-159">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-159">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="e7b9c-160">Personalizzare le route delle app</span><span class="sxs-lookup"><span data-stu-id="e7b9c-160">Customize app routes</span></span>

<span data-ttu-id="e7b9c-161">Per impostazione predefinita, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` la libreria usa le route mostrate nella tabella seguente per la rappresentazione di Stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-161">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="e7b9c-162">Route</span><span class="sxs-lookup"><span data-stu-id="e7b9c-162">Route</span></span>                            | <span data-ttu-id="e7b9c-163">Scopo</span><span class="sxs-lookup"><span data-stu-id="e7b9c-163">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="e7b9c-164">Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-164">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="e7b9c-165">Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-165">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="e7b9c-166">Visualizza i messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-166">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="e7b9c-167">Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-167">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="e7b9c-168">Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-168">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="e7b9c-169">Visualizza i messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-169">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="e7b9c-170">Indica che l'utente ha eseguito correttamente la disconnessione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-170">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="e7b9c-171">Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-171">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="e7b9c-172">Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-172">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="e7b9c-173">Le route visualizzate nella tabella precedente sono configurabili tramite `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-173">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="e7b9c-174">Quando si impostano le opzioni per fornire route personalizzate, verificare che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-174">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="e7b9c-175">Nell'esempio seguente, tutti i percorsi hanno il prefisso `/security`.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-175">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="e7b9c-176">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-176">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="e7b9c-177">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-177">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="e7b9c-178">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il `RemoteAuthenticatorView` rendering di con un parametro di azione esplicito:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-178">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="e7b9c-179">Se si sceglie di eseguire questa operazione, è possibile suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-179">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="e7b9c-180">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="e7b9c-180">Customize the authentication user interface</span></span>

<span data-ttu-id="e7b9c-181">`RemoteAuthenticatorView`include un set predefinito di elementi dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-181">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="e7b9c-182">Ogni stato può essere personalizzato passando un oggetto personalizzato `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-182">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="e7b9c-183">Per personalizzare il testo visualizzato durante il processo di accesso iniziale, può modificare `RemoteAuthenticatorView` il come indicato di seguito.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-183">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="e7b9c-184">`Authentication`componente (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e7b9c-184">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="e7b9c-185">`RemoteAuthenticatorView` Dispone di un frammento che può essere utilizzato per ogni route di autenticazione illustrato nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-185">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="e7b9c-186">Route</span><span class="sxs-lookup"><span data-stu-id="e7b9c-186">Route</span></span>                            | <span data-ttu-id="e7b9c-187">Frammento</span><span class="sxs-lookup"><span data-stu-id="e7b9c-187">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="e7b9c-188">Personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="e7b9c-188">Customize the user</span></span>

<span data-ttu-id="e7b9c-189">Gli utenti associati all'app possono essere personalizzati.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-189">Users bound to the app can be customized.</span></span> <span data-ttu-id="e7b9c-190">Nell'esempio seguente, tutti gli utenti autenticati ricevono un' `amr` attestazione per ognuno dei metodi di autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-190">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="e7b9c-191">Creare una classe che estende la `RemoteUserAccount` classe:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-191">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class OidcAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="e7b9c-192">Creare una factory che estende `AccountClaimsPrincipalFactory<TAccount>`:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-192">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<OidcAccount>
{
    public AccountClaimsPrincipalFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        OidcAccount account, RemoteAuthenticationUserOptions options)
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

<span data-ttu-id="e7b9c-193">Registrare i `CustomAccountFactory`servizi per usare:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-193">Register services to use the `CustomAccountFactory`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddApiAuthorization<RemoteAuthenticationState, OidcAccount>()
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, OidcAccount, 
        CustomAccountFactory>();
```

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="e7b9c-194">Supporto del prerendering con autenticazione</span><span class="sxs-lookup"><span data-stu-id="e7b9c-194">Support prerendering with authentication</span></span>

<span data-ttu-id="e7b9c-195">Dopo aver seguito le linee guida in uno degli Blazor argomenti dell'app webassembly ospitata, usare le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-195">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="e7b9c-196">Esegue il prerendering dei percorsi per i quali non è necessaria l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-196">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="e7b9c-197">Non esegue il prerendering dei percorsi per cui è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-197">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="e7b9c-198">Nella classe dell'app client `Program` (*Program.cs*), fattorizza le registrazioni del servizio comuni in un metodo separato, ad esempio `ConfigureCommonServices`:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-198">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
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

<span data-ttu-id="e7b9c-199">Nell'app `Startup.ConfigureServices`server registrare i servizi aggiuntivi seguenti:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-199">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="e7b9c-200">Nel `Startup.Configure` metodo dell'app server sostituire `endpoints.MapFallbackToFile("index.html")` con: `endpoints.MapFallbackToPage("/_Host")`</span><span class="sxs-lookup"><span data-stu-id="e7b9c-200">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="e7b9c-201">Nell'app Server creare una cartella *pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-201">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="e7b9c-202">Creare una pagina *_Host. cshtml* all'interno della cartella *pagine* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-202">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="e7b9c-203">Incollare il contenuto del file *wwwroot/index.html* dell'app client nel file *pages/_Host. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e7b9c-203">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="e7b9c-204">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-204">Update the file's contents:</span></span>

* <span data-ttu-id="e7b9c-205">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-205">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="e7b9c-206">Sostituire il `<app>Loading...</app>` tag con il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-206">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="e7b9c-207">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="e7b9c-207">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="e7b9c-208">Quando si esegue l'autenticazione e l'autorizzazione Blazor di un'app webassembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-208">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="e7b9c-209">Quello scelto dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-209">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="e7b9c-210">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-210">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="e7b9c-211">Autenticare gli utenti per chiamare solo le API di terze parti protette</span><span class="sxs-lookup"><span data-stu-id="e7b9c-211">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="e7b9c-212">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-212">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="e7b9c-213">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-213">In this scenario:</span></span>

* <span data-ttu-id="e7b9c-214">Il server che ospita l'app non svolge un ruolo.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-214">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="e7b9c-215">Le API nel server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-215">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="e7b9c-216">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-216">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="e7b9c-217">Autenticare gli utenti con un provider di terze parti e chiamare le API protette sul server host e la terza parte</span><span class="sxs-lookup"><span data-stu-id="e7b9c-217">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="e7b9c-218">Configurare l'identità con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-218">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="e7b9c-219">Ottenere i token necessari per l'accesso all'API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-219">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="e7b9c-220">Quando un utente esegue l'accesso, Identity raccoglie i token di accesso e di aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-220">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="e7b9c-221">A questo punto, sono disponibili due approcci per eseguire chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-221">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="e7b9c-222">Usare un token di accesso al server per recuperare il token di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="e7b9c-222">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="e7b9c-223">Usare il token di accesso generato sul server per recuperare il token di accesso di terze parti da un endpoint API server.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-223">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="e7b9c-224">Da qui, usare il token di accesso di terze parti per chiamare le risorse dell'API di terze parti direttamente dall'identità nel client.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-224">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="e7b9c-225">Questo approccio non è consigliato.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-225">We don't recommend this approach.</span></span> <span data-ttu-id="e7b9c-226">Questo approccio richiede di trattare il token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-226">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="e7b9c-227">In termini di OAuth, l'app pubblica non ha un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-227">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="e7b9c-228">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-228">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="e7b9c-229">Al token di accesso di terze parti potrebbero essere stati concessi ulteriori ambiti per eseguire operazioni sensibili in base al fatto che la terza parte ha emesso il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-229">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="e7b9c-230">Analogamente, i token di aggiornamento non devono essere rilasciati a un client che non è considerato attendibile, in quanto in questo modo si ottiene l'accesso illimitato ai client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-230">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="e7b9c-231">Eseguire chiamate API dal client all'API server per chiamare le API di terze parti</span><span class="sxs-lookup"><span data-stu-id="e7b9c-231">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="e7b9c-232">Eseguire una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-232">Make an API call from the client to the server API.</span></span> <span data-ttu-id="e7b9c-233">Dal server recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-233">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="e7b9c-234">Sebbene questo approccio richieda un hop di rete aggiuntivo tramite il server per la chiamata di un'API di terze parti, in definitiva si ottiene un'esperienza più sicura:</span><span class="sxs-lookup"><span data-stu-id="e7b9c-234">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="e7b9c-235">Il server può archiviare i token di aggiornamento e assicurarsi che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-235">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="e7b9c-236">L'app non può perdere i token di accesso dal server che potrebbe contenere autorizzazioni più sensibili.</span><span class="sxs-lookup"><span data-stu-id="e7b9c-236">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
