---
title: ASP.NET Blazor scenari di sicurezza aggiuntivi di WebAssembly di base
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501122"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="0f425-102">ASP.NET Core Blazor WebAssembly scenari di sicurezza aggiuntivi</span><span class="sxs-lookup"><span data-stu-id="0f425-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="0f425-103">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="0f425-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="0f425-104">Richiedere token di accesso aggiuntiviRequest additional access tokens</span><span class="sxs-lookup"><span data-stu-id="0f425-104">Request additional access tokens</span></span>

<span data-ttu-id="0f425-105">La maggior parte delle app richiede solo un token di accesso per interagire con le risorse protette usate.</span><span class="sxs-lookup"><span data-stu-id="0f425-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="0f425-106">In alcuni scenari, un'app potrebbe richiedere più di un token per interagire con due o più risorse.</span><span class="sxs-lookup"><span data-stu-id="0f425-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="0f425-107">Nell'esempio seguente, gli ambiti aggiuntivi dell'API Microsoft Graph di Azure Active Directory (AAD) sono necessari a un'app per leggere i dati utente e inviare posta.</span><span class="sxs-lookup"><span data-stu-id="0f425-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="0f425-108">Dopo aver aggiunto le autorizzazioni dell'API Microsoft Graph nel portale di Azure`Program.Main`AAD, gli ambiti aggiuntivi vengono configurati nell'app Client ( *, Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0f425-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="0f425-109">Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di eseguire il provisioning di un token con un determinato set di ambiti, come illustrato nell'esempio seguente:The method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span><span class="sxs-lookup"><span data-stu-id="0f425-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="0f425-110">`TryGetToken`Restituisce:</span><span class="sxs-lookup"><span data-stu-id="0f425-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="0f425-111">`true`con `token` l'uso.</span><span class="sxs-lookup"><span data-stu-id="0f425-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="0f425-112">`false`se il token non viene recuperato.</span><span class="sxs-lookup"><span data-stu-id="0f425-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="0f425-113">Gestire gli errori di richiesta di tokenHandle token request errors</span><span class="sxs-lookup"><span data-stu-id="0f425-113">Handle token request errors</span></span>

<span data-ttu-id="0f425-114">Quando un'applicazione a pagina singola (SPA) autentica un utente utilizzando Open ID Connect (OIDC), lo stato di autenticazione viene mantenuto localmente all'interno della SPA e nel provider di identità (IP) sotto forma di cookie di sessione che viene impostato come risultato dell'utente che fornisce le proprie credenziali.</span><span class="sxs-lookup"><span data-stu-id="0f425-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="0f425-115">I token che l'IP genera per l'utente sono in genere validi per brevi periodi di tempo, circa un'ora normalmente, pertanto l'app client deve recuperare regolarmente nuovi token.</span><span class="sxs-lookup"><span data-stu-id="0f425-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="0f425-116">In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi.</span><span class="sxs-lookup"><span data-stu-id="0f425-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="0f425-117">Nella maggior parte dei casi, i client OIDC sono in grado di eseguire il provisioning di nuovi token senza richiedere all'utente di eseguire nuovamente l'autenticazione grazie allo stato di autenticazione o "sessione" che viene mantenuto all'interno dell'IP.</span><span class="sxs-lookup"><span data-stu-id="0f425-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="0f425-118">Esistono alcuni casi in cui il client non può ottenere un token senza l'interazione dell'utente, ad esempio, quando per qualche motivo l'utente si disconnette in modo esplicito dall'IP.</span><span class="sxs-lookup"><span data-stu-id="0f425-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="0f425-119">Questo scenario si verifica `https://login.microsoftonline.com` se un utente visita e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Qualsiasi token che contiene il client potrebbe non essere più valido.</span><span class="sxs-lookup"><span data-stu-id="0f425-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="0f425-120">Inoltre, il client non è in grado di eseguire il provisioning di un nuovo token senza l'interazione dell'utente dopo la scadenza del token corrente.</span><span class="sxs-lookup"><span data-stu-id="0f425-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="0f425-121">Questi scenari non sono specifici per l'autenticazione basata su token.</span><span class="sxs-lookup"><span data-stu-id="0f425-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="0f425-122">Fanno parte della natura delle SPA.</span><span class="sxs-lookup"><span data-stu-id="0f425-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="0f425-123">Una SPA che utilizza i cookie non riesce a chiamare un'API del server se il cookie di autenticazione viene rimosso.</span><span class="sxs-lookup"><span data-stu-id="0f425-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="0f425-124">Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:When an app performs API calls to protected resources, you must be aware of the following:</span><span class="sxs-lookup"><span data-stu-id="0f425-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="0f425-125">Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, all'utente potrebbe essere richiesto di eseguire nuovamente l'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="0f425-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="0f425-126">Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.</span><span class="sxs-lookup"><span data-stu-id="0f425-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="0f425-127">Quando l'app richiede un token, esistono due possibili risultati:When the app requests a token, there are two possible outcomes:</span><span class="sxs-lookup"><span data-stu-id="0f425-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="0f425-128">La richiesta ha esito positivo e l'app dispone di un token valido.</span><span class="sxs-lookup"><span data-stu-id="0f425-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="0f425-129">La richiesta ha esito negativo e l'app deve autenticare nuovamente l'utente per ottenere un nuovo token.</span><span class="sxs-lookup"><span data-stu-id="0f425-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="0f425-130">Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="0f425-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="0f425-131">Esistono diversi approcci con livelli crescenti di complessità:</span><span class="sxs-lookup"><span data-stu-id="0f425-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="0f425-132">Archiviare lo stato della pagina corrente nell'archivio della sessione.</span><span class="sxs-lookup"><span data-stu-id="0f425-132">Store the current page state in session storage.</span></span> <span data-ttu-id="0f425-133">Durante `OnInitializeAsync`, verificare se lo stato può essere ripristinato prima di continuare.</span><span class="sxs-lookup"><span data-stu-id="0f425-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="0f425-134">Aggiungi un parametro di stringa di query e usalo per segnalare all'app che deve idratare nuovamente lo stato salvato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="0f425-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="0f425-135">Aggiungere un parametro di stringa di query con un identificatore univoco per archiviare i dati nell'archivio di sessione senza rischiare conflitti con altri elementi.</span><span class="sxs-lookup"><span data-stu-id="0f425-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="0f425-136">L'esempio seguente illustra come:</span><span class="sxs-lookup"><span data-stu-id="0f425-136">The following example shows how to:</span></span>

* <span data-ttu-id="0f425-137">Mantenere lo stato prima del reindirizzamento alla pagina di accesso.</span><span class="sxs-lookup"><span data-stu-id="0f425-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="0f425-138">Ripristinare l'autenticazione successiva dello stato precedente utilizzando il parametro della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="0f425-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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
            await httpClient.PostJsonAsync("Save", User);
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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="0f425-139">Salvare lo stato dell'app prima di un'operazione di autenticazioneSave app state before an authentication operation</span><span class="sxs-lookup"><span data-stu-id="0f425-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="0f425-140">Durante un'operazione di autenticazione, in alcuni casi si desidera salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP.</span><span class="sxs-lookup"><span data-stu-id="0f425-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="0f425-141">Questo può essere il caso quando si utilizza qualcosa come un contenitore di stato e si desidera ripristinare lo stato dopo l'autenticazione ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="0f425-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="0f425-142">È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare tale stato una volta completata correttamente l'operazione di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="0f425-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="0f425-143">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="0f425-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="0f425-144">Personalizzare i percorsi delle app</span><span class="sxs-lookup"><span data-stu-id="0f425-144">Customize app routes</span></span>

<span data-ttu-id="0f425-145">Per impostazione `Microsoft.AspNetCore.Components.WebAssembly.Authentication` predefinita, la libreria utilizza le route illustrate nella tabella seguente per rappresentare stati di autenticazione diversi.</span><span class="sxs-lookup"><span data-stu-id="0f425-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="0f425-146">Route</span><span class="sxs-lookup"><span data-stu-id="0f425-146">Route</span></span>                            | <span data-ttu-id="0f425-147">Scopo</span><span class="sxs-lookup"><span data-stu-id="0f425-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="0f425-148">Attiva un'operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="0f425-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="0f425-149">Gestisce il risultato di qualsiasi operazione di accesso.</span><span class="sxs-lookup"><span data-stu-id="0f425-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="0f425-150">Visualizza messaggi di errore quando l'operazione di accesso non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="0f425-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="0f425-151">Attiva un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="0f425-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="0f425-152">Gestisce il risultato di un'operazione di disconnessione.</span><span class="sxs-lookup"><span data-stu-id="0f425-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="0f425-153">Visualizza messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo.</span><span class="sxs-lookup"><span data-stu-id="0f425-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="0f425-154">Indica che l'utente si è disconnetteto correttamente.</span><span class="sxs-lookup"><span data-stu-id="0f425-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="0f425-155">Attiva un'operazione per modificare il profilo utente.</span><span class="sxs-lookup"><span data-stu-id="0f425-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="0f425-156">Attiva un'operazione per registrare un nuovo utente.</span><span class="sxs-lookup"><span data-stu-id="0f425-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="0f425-157">I percorsi illustrati nella tabella precedente `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`sono configurabili tramite .</span><span class="sxs-lookup"><span data-stu-id="0f425-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="0f425-158">Quando imposti le opzioni per fornire route personalizzate, verifica che l'app disponga di una route che gestisce ogni percorso.</span><span class="sxs-lookup"><span data-stu-id="0f425-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="0f425-159">Nell'esempio seguente, tutti i percorsi sono preceduti da `/security`.</span><span class="sxs-lookup"><span data-stu-id="0f425-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="0f425-160">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="0f425-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="0f425-161">`Program.Main`(*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="0f425-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="0f425-162">Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering di con un parametro di azione esplicito:If the requirement calls for completely different routes, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span><span class="sxs-lookup"><span data-stu-id="0f425-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="0f425-163">Se scegli di farlo, puoi suddividere l'interfaccia utente in pagine diverse.</span><span class="sxs-lookup"><span data-stu-id="0f425-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="0f425-164">Personalizzare l'interfaccia utente di autenticazione</span><span class="sxs-lookup"><span data-stu-id="0f425-164">Customize the authentication user interface</span></span>

<span data-ttu-id="0f425-165">`RemoteAuthenticatorView`include un set predefinito di parti dell'interfaccia utente per ogni stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="0f425-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="0f425-166">Ogni stato può essere personalizzato passando `RenderFragment`un oggetto personalizzato .</span><span class="sxs-lookup"><span data-stu-id="0f425-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="0f425-167">Per personalizzare il testo visualizzato durante il processo `RemoteAuthenticatorView` di accesso iniziale, è possibile modificare il seguente.</span><span class="sxs-lookup"><span data-stu-id="0f425-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="0f425-168">`Authentication`componente (*Pages/Authentication.razor*):</span><span class="sxs-lookup"><span data-stu-id="0f425-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="0f425-169">Il `RemoteAuthenticatorView` ha un frammento che può essere utilizzato per ogni route di autenticazione illustrata nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="0f425-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="0f425-170">Route</span><span class="sxs-lookup"><span data-stu-id="0f425-170">Route</span></span>                            | <span data-ttu-id="0f425-171">Frammento</span><span class="sxs-lookup"><span data-stu-id="0f425-171">Fragment</span></span>                |
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
