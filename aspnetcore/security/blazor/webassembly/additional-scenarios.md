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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly scenari di sicurezza aggiuntivi

Di [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Richiedere token di accesso aggiuntiviRequest additional access tokens

La maggior parte delle app richiede solo un token di accesso per interagire con le risorse protette usate. In alcuni scenari, un'app potrebbe richiedere più di un token per interagire con due o più risorse.

Nell'esempio seguente, gli ambiti aggiuntivi dell'API Microsoft Graph di Azure Active Directory (AAD) sono necessari a un'app per leggere i dati utente e inviare posta. Dopo aver aggiunto le autorizzazioni dell'API Microsoft Graph nel portale di Azure`Program.Main`AAD, gli ambiti aggiuntivi vengono configurati nell'app Client ( *, Program.cs*):

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

Il `IAccessTokenProvider.RequestToken` metodo fornisce un overload che consente a un'app di eseguire il provisioning di un token con un determinato set di ambiti, come illustrato nell'esempio seguente:The method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:

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

`TryGetToken`Restituisce:

* `true`con `token` l'uso.
* `false`se il token non viene recuperato.

## <a name="handle-token-request-errors"></a>Gestire gli errori di richiesta di tokenHandle token request errors

Quando un'applicazione a pagina singola (SPA) autentica un utente utilizzando Open ID Connect (OIDC), lo stato di autenticazione viene mantenuto localmente all'interno della SPA e nel provider di identità (IP) sotto forma di cookie di sessione che viene impostato come risultato dell'utente che fornisce le proprie credenziali.

I token che l'IP genera per l'utente sono in genere validi per brevi periodi di tempo, circa un'ora normalmente, pertanto l'app client deve recuperare regolarmente nuovi token. In caso contrario, l'utente verrà disconnesso dopo la scadenza dei token concessi. Nella maggior parte dei casi, i client OIDC sono in grado di eseguire il provisioning di nuovi token senza richiedere all'utente di eseguire nuovamente l'autenticazione grazie allo stato di autenticazione o "sessione" che viene mantenuto all'interno dell'IP.

Esistono alcuni casi in cui il client non può ottenere un token senza l'interazione dell'utente, ad esempio, quando per qualche motivo l'utente si disconnette in modo esplicito dall'IP. Questo scenario si verifica `https://login.microsoftonline.com` se un utente visita e si disconnette. In questi scenari, l'app non sa immediatamente che l'utente si è disconnesso. Qualsiasi token che contiene il client potrebbe non essere più valido. Inoltre, il client non è in grado di eseguire il provisioning di un nuovo token senza l'interazione dell'utente dopo la scadenza del token corrente.

Questi scenari non sono specifici per l'autenticazione basata su token. Fanno parte della natura delle SPA. Una SPA che utilizza i cookie non riesce a chiamare un'API del server se il cookie di autenticazione viene rimosso.

Quando un'app esegue chiamate API a risorse protette, è necessario tenere presente quanto segue:When an app performs API calls to protected resources, you must be aware of the following:

* Per eseguire il provisioning di un nuovo token di accesso per chiamare l'API, all'utente potrebbe essere richiesto di eseguire nuovamente l'autenticazione.
* Anche se il client dispone di un token che sembra essere valido, la chiamata al server potrebbe non riuscire perché il token è stato revocato dall'utente.

Quando l'app richiede un token, esistono due possibili risultati:When the app requests a token, there are two possible outcomes:

* La richiesta ha esito positivo e l'app dispone di un token valido.
* La richiesta ha esito negativo e l'app deve autenticare nuovamente l'utente per ottenere un nuovo token.

Quando una richiesta di token ha esito negativo, è necessario decidere se si desidera salvare lo stato corrente prima di eseguire un reindirizzamento. Esistono diversi approcci con livelli crescenti di complessità:

* Archiviare lo stato della pagina corrente nell'archivio della sessione. Durante `OnInitializeAsync`, verificare se lo stato può essere ripristinato prima di continuare.
* Aggiungi un parametro di stringa di query e usalo per segnalare all'app che deve idratare nuovamente lo stato salvato in precedenza.
* Aggiungere un parametro di stringa di query con un identificatore univoco per archiviare i dati nell'archivio di sessione senza rischiare conflitti con altri elementi.

L'esempio seguente illustra come:

* Mantenere lo stato prima del reindirizzamento alla pagina di accesso.
* Ripristinare l'autenticazione successiva dello stato precedente utilizzando il parametro della stringa di query.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Salvare lo stato dell'app prima di un'operazione di autenticazioneSave app state before an authentication operation

Durante un'operazione di autenticazione, in alcuni casi si desidera salvare lo stato dell'app prima che il browser venga reindirizzato all'indirizzo IP. Questo può essere il caso quando si utilizza qualcosa come un contenitore di stato e si desidera ripristinare lo stato dopo l'autenticazione ha esito positivo. È possibile usare un oggetto stato di autenticazione personalizzato per mantenere lo stato specifico dell'app o un riferimento a esso e ripristinare tale stato una volta completata correttamente l'operazione di autenticazione.

`Authentication`componente (*Pages/Authentication.razor*):

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

## <a name="customize-app-routes"></a>Personalizzare i percorsi delle app

Per impostazione `Microsoft.AspNetCore.Components.WebAssembly.Authentication` predefinita, la libreria utilizza le route illustrate nella tabella seguente per rappresentare stati di autenticazione diversi.

| Route                            | Scopo |
| -------------------------------- | ------- |
| `authentication/login`           | Attiva un'operazione di accesso. |
| `authentication/login-callback`  | Gestisce il risultato di qualsiasi operazione di accesso. |
| `authentication/login-failed`    | Visualizza messaggi di errore quando l'operazione di accesso non riesce per qualche motivo. |
| `authentication/logout`          | Attiva un'operazione di disconnessione. |
| `authentication/logout-callback` | Gestisce il risultato di un'operazione di disconnessione. |
| `authentication/logout-failed`   | Visualizza messaggi di errore quando l'operazione di disconnessione non riesce per qualche motivo. |
| `authentication/logged-out`      | Indica che l'utente si è disconnetteto correttamente. |
| `authentication/profile`         | Attiva un'operazione per modificare il profilo utente. |
| `authentication/register`        | Attiva un'operazione per registrare un nuovo utente. |

I percorsi illustrati nella tabella precedente `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`sono configurabili tramite . Quando imposti le opzioni per fornire route personalizzate, verifica che l'app disponga di una route che gestisce ogni percorso.

Nell'esempio seguente, tutti i percorsi sono preceduti da `/security`.

`Authentication`componente (*Pages/Authentication.razor*):

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

Se il requisito richiede percorsi completamente diversi, impostare le route come descritto in precedenza ed eseguire il rendering di con un parametro di azione esplicito:If the requirement calls for completely different routes, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Se scegli di farlo, puoi suddividere l'interfaccia utente in pagine diverse.

## <a name="customize-the-authentication-user-interface"></a>Personalizzare l'interfaccia utente di autenticazione

`RemoteAuthenticatorView`include un set predefinito di parti dell'interfaccia utente per ogni stato di autenticazione. Ogni stato può essere personalizzato passando `RenderFragment`un oggetto personalizzato . Per personalizzare il testo visualizzato durante il processo `RemoteAuthenticatorView` di accesso iniziale, è possibile modificare il seguente.

`Authentication`componente (*Pages/Authentication.razor*):

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

Il `RemoteAuthenticatorView` ha un frammento che può essere utilizzato per ogni route di autenticazione illustrata nella tabella seguente.

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
