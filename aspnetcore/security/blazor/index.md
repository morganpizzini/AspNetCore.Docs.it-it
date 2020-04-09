---
title: autenticazione Blazor e autorizzazione di ASP.NET Core
author: guardrex
description: Informazioni Blazor sugli scenari di autenticazione e autorizzazione.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501320"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>autenticazione Blazor e autorizzazione di ASP.NET Core

Di [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> Per le indicazioni contenute Blazor in questo articolo Blazor che si applicano a WebAssembly, è necessario il modello WebAssembly di ASP.NET versione 3.2 o successiva. Se non si utilizza Visual Studio versione 16.6 Preview Blazor 2 o versione successiva, <xref:blazor/get-started>ottenere il modello WebAssembly più recente seguendo le indicazioni riportate in .

ASP.NET Core supporta la configurazione e Blazor la gestione della sicurezza nelle app.

Gli scenari Blazor di Blazor sicurezza differiscono tra le app Server e WebAssembly.Security scenarios differ between Server and WebAssembly apps. Poiché Blazor le app server vengono eseguite nel server, i controlli di autorizzazione sono in grado di determinare:Because Server apps run on the server, authorization checks are able to determine:

* Le opzioni dell'interfaccia utente presentate all'utente (ad esempio, le voci di menu disponibili per un utente).
* Le regole di accesso per le aree dell'app e i componenti.

BlazorLe app WebAssembly vengono eseguite sul client. L'autorizzazione viene usata *solo* per determinare quali opzioni dell'interfaccia utente visualizzare. Poiché i controlli sul lato client possono Blazor essere modificati o ignorati da un utente, un'app WebAssembly non può applicare le regole di accesso di autorizzazione.

[Le convenzioni](xref:security/authorization/razor-pages-authorization) di autorizzazione di Razor Pages non si applicano ai componenti Razor instradabili. Se un componente Razor non instradabile è [incorporato in una pagina](xref:blazor/integrate-components#render-components-from-a-page-or-view), le convenzioni di autorizzazione della pagina influiscono indirettamente sul componente Razor insieme al resto del contenuto della pagina.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> non sono supportati nei componenti Razor.

## <a name="authentication"></a>Authentication

Blazorutilizza i meccanismi di autenticazione ASP.NET Core esistenti per stabilire l'identità dell'utente. Il meccanismo esatto Blazor dipende dalla modalità di hosting dell'app, WebAssembly o Server.The exact mechanism depends on how the app is hosted, Blazor WebAssembly or Blazor Server.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorAutenticazione WebAssembly

Nelle Blazor app WebAssembly i controlli di autenticazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti. Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.

Aggiungere quanto segue:

* Un riferimento al pacchetto per [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) al file di progetto dell'app.
* Spazio `Microsoft.AspNetCore.Components.Authorization` dei nomi nel file *_Imports.razor* dell'app.

Per gestire l'autenticazione, l'implementazione di un servizio predefinito o personalizzato `AuthenticationStateProvider` è descritta nelle sezioni seguenti.

Per ulteriori informazioni sulla creazione <xref:security/blazor/webassembly/index>di app e configurazione, vedere .

### <a name="opno-locblazor-server-authentication"></a>BlazorAutenticazione server

BlazorLe app server operano su una connessione SignalRin tempo reale creata utilizzando . [L'autenticazione nelle SignalRapp basate sulle app](xref:signalr/authn-and-authz) viene gestita quando viene stabilita la connessione. L'autenticazione può basarsi su un cookie o su altri bearer token.

Per ulteriori informazioni sulla creazione <xref:security/blazor/server>di app e configurazione, vedere .

## <a name="authenticationstateprovider-service"></a>Servizio AuthenticationStateProvider

Il `AuthenticationStateProvider` servizio predefinito ottiene i dati sullo stato `HttpContext.User`di autenticazione da ASP.NET Core. Questo è il modo in cui lo stato di autenticazione si integra con i meccanismi di autenticazione ASP.NET core esistenti.

`AuthenticationStateProvider` è il servizio sottostante usato dal componente `AuthorizeView` e dal componente `CascadingAuthenticationState` per ottenere lo stato di autenticazione.

In genere non si usa `AuthenticationStateProvider` direttamente. Usare il [componente AuthorizeView](#authorizeview-component) o [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approcci descritti più avanti in questo articolo. Lo svantaggio principale dell'uso diretto di `AuthenticationStateProvider` è che il componente non riceve alcuna notifica automaticamente se i dati relativi allo stato di autenticazione sottostanti cambiano.

Il servizio `AuthenticationStateProvider` può fornire i dati <xref:System.Security.Claims.ClaimsPrincipal> dell'utente corrente, come illustrato nell'esempio seguente:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Se `user.Identity.IsAuthenticated` è `true` e perché l'utente è un <xref:System.Security.Claims.ClaimsPrincipal>, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.

Per altre informazioni sull'inserimento delle dipendenze e sui servizi, vedere <xref:blazor/dependency-injection> e <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementare un AuthenticationStateProvider personalizzato

Se l'app richiede un `AuthenticationStateProvider` provider `GetAuthenticationStateAsync`personalizzato, implementare ed eseguire l'override di :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

In Blazor un'app WebAssembly il `CustomAuthStateProvider` `Main` servizio è registrato in *Program.cs:*

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

In Blazor un'app `CustomAuthStateProvider` Server, il `Startup.ConfigureServices`servizio è registrato in:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Utilizzando `CustomAuthStateProvider` l'esempio precedente, tutti gli utenti vengono `mrfibuli`autenticati con il nome utente .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Esporre lo stato di autenticazione come un parametro a catena

Se i dati dello stato di autenticazione sono necessari per la logica procedurale, ad esempio quando si esegue un'azione attivata dall'utente, ottenere i dati dello stato di autenticazione definendo un parametro a catena di tipo `Task<AuthenticationState>`:

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

Se `user.Identity.IsAuthenticated` è `true`, è possibile enumerare le attestazioni e valutare l'appartenenza ai ruoli.

Impostare `Task<AuthenticationState>` il parametro `AuthorizeRouteView` a `CascadingAuthenticationState` catena `App` utilizzando i componenti e nel componente (*App.razor*):

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

In Blazor un'app WebAssembly aggiungere servizi `Program.Main`per le opzioni e l'autorizzazione a:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

In Blazor un'app Server i servizi per le opzioni e l'autorizzazione sono già presenti, pertanto non sono necessarie altre azioni.

## <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione di un utente, vengono applicate le regole di *autorizzazione* per controllare le operazioni consentite per un utente.

L'accesso viene in genere concesso o negato in base alle condizioni seguenti:

* L'utente è autenticato (ha eseguito l'accesso).
* L'utente è incluso in un *ruolo*.
* L'utente ha un'*attestazione*.
* I *criteri* sono soddisfatti.

Questi concetti sono uguali a quelli validi per un'app ASP.NET Core MVC o Razor Pages. Per altre informazioni sulla sicurezza di ASP.NET Core, vedere gli articoli in [Sicurezza e identità per ASP.NET Core](xref:security/index).

## <a name="authorizeview-component"></a>Componente AuthorizeView

Il componente `AuthorizeView` visualizza in modo selettivo l'interfaccia utente a seconda del fatto che l'utente sia autorizzato a visualizzarla. Questo approccio è utile quando è necessario solo *visualizzare* dati per l'utente e non occorre usare l'identità dell'utente nella logica procedurale.

Il componente espone una variabile `context` di tipo `AuthenticationState`, che è possibile usare per accedere alle informazioni sull'utente connesso:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Se l'utente non è autenticato, è anche possibile fornire un contenuto diverso per la visualizzazione:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

Il `AuthorizeView` componente può essere `NavMenu` utilizzato nel componente (*Shared/NavMenu.razor*) per visualizzare un elemento di elenco (`<li>...</li>`) per un `NavLink`oggetto , ma questo approccio rimuove solo l'elemento dell'elenco dall'output di cui è stato eseguito il rendering. Non impedisce all'utente di passare al componente.

Il contenuto `<Authorized>` `<NotAuthorized>` e i tag possono includere elementi arbitrari, ad esempio altri componenti interattivi.

Le condizioni di autorizzazione, ad esempio i ruoli o i criteri che consentono di controllare le opzioni dell'interfaccia utente o l'accesso, sono presentate nella sezione [Autorizzazione](#authorization).

Se non sono specificate condizioni di autorizzazione, `AuthorizeView` usa criteri predefiniti e considera:

* Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.
* Gli utenti non autenticati (disconnessi) come non autorizzati.

### <a name="role-based-and-policy-based-authorization"></a>Autorizzazione basata sui ruoli e basata sui criteri

Il componente `AuthorizeView` supporta l'autorizzazione *basata sui ruoli* oppure *basata sui criteri*.

Per l'autorizzazione basata sui ruoli, usare il parametro `Roles`:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Per altre informazioni, vedere <xref:security/authorization/roles>.

Per l'autorizzazione basata sui criteri, usare il parametro `Policy`:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

L'autorizzazione basata sulle attestazioni è un caso speciale di autorizzazione basata su criteri. Ad esempio, è possibile definire un criterio che richiede che gli utenti abbiano una determinata attestazione. Per altre informazioni, vedere <xref:security/authorization/policies>.

Queste API possono essere usate Blazor nelle Blazor app Server o WebAssembly.These APIs can be used in either Server or WebAssembly apps.

Se non si specifica `Roles` o `Policy`, `AuthorizeView` usa i criteri predefiniti.

### <a name="content-displayed-during-asynchronous-authentication"></a>Contenuto visualizzato durante l'autenticazione asincrona

Blazorconsente di determinare lo stato di autenticazione *in modo asincrono.* Lo scenario principale per Blazor questo approccio è nelle app WebAssembly che effettuano una richiesta a un endpoint esterno per l'autenticazione.

Mentre è in corso l'autenticazione `AuthorizeView` non visualizza alcun contenuto per impostazione predefinita. Per visualizzare contenuto durante l'autenticazione, usare l'elemento `<Authorizing>`:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Questo approccio non è Blazor in genere applicabile alle app server. BlazorLe app server conoscono lo stato di autenticazione non appena viene stabilito lo stato. `Authorizing`il contenuto può Blazor essere fornito `AuthorizeView` nel componente di un'app Server, ma il contenuto non viene mai visualizzato.

## <a name="authorize-attribute"></a>Attributo [Authorize]

L'attributo `[Authorize]` può essere utilizzato nei componenti Razor:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Utilizzare `[Authorize]` solo `@page` i componenti Blazor raggiunti tramite il Router. L'autorizzazione viene eseguita solo come un aspetto del routing e *non* per i componenti figlio di cui viene eseguito il rendering all'interno di una pagina. Per autorizzare la visualizzazione di parti specifiche all'interno di una pagina, usare invece `AuthorizeView`.

L'attributo `[Authorize]` supporta anche l'autorizzazione basata sui ruoli o basata sui criteri. Per l'autorizzazione basata sui ruoli, usare il parametro `Roles`:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Per l'autorizzazione basata sui criteri, usare il parametro `Policy`:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se non si specifica `Roles` o `Policy`, `[Authorize]` usa i criteri predefiniti, che per impostazione predefinita considerano:

* Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.
* Gli utenti non autenticati (disconnessi) come non autorizzati.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizzare il contenuto non autorizzato con il componente Router

Il `Router` componente, insieme `AuthorizeRouteView` al componente, consente all'app di specificare contenuto personalizzato se:

* Non viene trovato contenuto.
* L'utente non supera una condizione `[Authorize]` applicata al componente. L'attributo `[Authorize]` viene trattato nella sezione [relativa all'attributo. `[Authorize]` ](#authorize-attribute)
* L'autenticazione asincrona è in corso.

Nel modello Blazor di progetto `App` Server predefinito, il componente (*App.razor*) illustra come impostare il contenuto personalizzato:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Il contenuto `<NotFound>` `<NotAuthorized>`dei `<Authorizing>` tag , e possono includere elementi arbitrari, ad esempio altri componenti interattivi.

Se `<NotAuthorized>` l'elemento non è `AuthorizeRouteView` specificato, viene utilizzato il seguente messaggio di fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notifica per le modifiche dello stato di autenticazione

Se l'app determina che i dati dello stato di autenticazione sottostanti sono stati modificati (ad esempio, perché `NotifyAuthenticationStateChanged` l'utente si è disconnesso o un altro utente ha modificato i ruoli), un [AuthenticationStateProvider personalizzato](#implement-a-custom-authenticationstateprovider) può facoltativamente richiamare il metodo nella classe `AuthenticationStateProvider` di base. Viene così inviata notifica ai consumer dei dati di stato di autenticazione (ad esempio, `AuthorizeView`) di eseguire nuovamente il rendering usando i nuovi dati.

## <a name="procedural-logic"></a>Logica procedurale

Se l'app deve controllare le regole di autorizzazione come parte della logica procedurale, usare un parametro a catena di tipo `Task<AuthenticationState>` per ottenere il <xref:System.Security.Claims.ClaimsPrincipal> dell'utente. `Task<AuthenticationState>` può essere combinato con altri servizi, ad esempio `IAuthorizationService`, per valutare i criteri.

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> In Blazor un componente dell'app `Microsoft.AspNetCore.Authorization` `Microsoft.AspNetCore.Components.Authorization` WebAssembly aggiungere gli spazi dei nomi e :
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Questi spazi dei nomi possono essere forniti a livello globale aggiungendoli al file *_Imports.razor* dell'app.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>Autorizzazione Blazor nelle app WebAssembly

Nelle Blazor app WebAssembly, i controlli di autorizzazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti. Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.

**Eseguire sempre i controlli di autorizzazione nel server all'interno degli eventuali endpoint dell'API a cui accede l'app sul lato client.**

Per ulteriori informazioni, vedere <xref:security/blazor/webassembly/index>gli articoli in .

## <a name="troubleshoot-errors"></a>Risolvere gli errori

Errori comuni:

* **L'autorizzazione richiede un parametro a catena di tipo Task\<AuthenticationState>. Considerare l'utilizzo di CascadingAuthenticationState per fornire questo.**

* **`null`viene ricevuto valore per`authenticationStateTask`**

È probabile che il progetto non sia Blazor stato creato utilizzando un modello Server con l'autenticazione abilitata. Eseguire `<CascadingAuthenticationState>` il wrapping di una parte intorno `App` a una parte dell'albero dell'interfaccia utente, ad esempio nel componente (*App.razor*) come segue:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

`CascadingAuthenticationState` fornisce il parametro a catena `Task<AuthenticationState>`, ricevuto a sua volta dal servizio di inserimento delle dipendenze `AuthenticationStateProvider` sottostante.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Impressionante Blazor: Collegamenti](https://github.com/AdrienTorris/awesome-blazor#authentication) di esempio della community di autenticazione
