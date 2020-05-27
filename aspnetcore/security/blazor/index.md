---
title: "ASP.NET Core Blazor autenticazione e autorizzazione" autore: Descrizione: "informazioni sugli Blazor scenari di autenticazione e autorizzazione".
monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID '': 

---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a>BlazorAutenticazione e autorizzazione ASP.NET Core

Di [Steve Sanderson](https://github.com/SteveSandersonMS) e [Luke Latham](https://github.com/guardrex)

ASP.NET Core supporta la configurazione e la gestione della sicurezza nelle Blazor app.

Gli scenari di sicurezza sono diversi tra Blazor le app Server e Blazor webassembly. Poiché Blazor le app Server vengono eseguite nel server, i controlli delle autorizzazioni sono in grado di determinare:

* Le opzioni dell'interfaccia utente presentate all'utente (ad esempio, le voci di menu disponibili per un utente).
* Le regole di accesso per le aree dell'app e i componenti.

BlazorLe app webassembly vengono eseguite sul client. L'autorizzazione viene usata *solo* per determinare quali opzioni dell'interfaccia utente visualizzare. Poiché i controlli lato client possono essere modificati o ignorati da un utente, un' Blazor app webassembly non può applicare le regole di accesso all'autorizzazione.

Le [ Razor convenzioni di autorizzazione pagine](xref:security/authorization/razor-pages-authorization) non si applicano ai componenti instradabili Razor . Se un componente non instradabile Razor è [incorporato in una pagina](xref:blazor/integrate-components#render-components-from-a-page-or-view), le convenzioni di autorizzazione della pagina influiscono indirettamente sul Razor componente insieme al resto del contenuto della pagina.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>e <xref:Microsoft.AspNetCore.Identity.UserManager%601> non sono supportati nei Razor componenti di.

## <a name="authentication"></a>Authentication

BlazorUSA i meccanismi di autenticazione ASP.NET Core esistenti per stabilire l'identità dell'utente. Il meccanismo esatto dipende dal modo in cui l' Blazor app è ospitata, Blazor webassembly o Blazor Server.

### <a name="blazor-webassembly-authentication"></a>BlazorAutenticazione webassembly

Nelle Blazor app webassembly i controlli di autenticazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti. Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.

Aggiungere quanto segue:

* Riferimento al pacchetto per [Microsoft. AspNetCore. Components. Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) per il file di progetto dell'app.
* `Microsoft.AspNetCore.Components.Authorization`Spazio dei nomi del file *_Imports. Razor* dell'app.

Per gestire l'autenticazione, l'implementazione di un servizio incorporato o personalizzato è illustrata <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> nelle sezioni seguenti.

Per ulteriori informazioni sulla creazione di app e configurazione, vedere <xref:security/blazor/webassembly/index> .

### <a name="blazor-server-authentication"></a>BlazorAutenticazione server

BlazorLe app server operano su una connessione in tempo reale creata con SignalR . L' [autenticazione in SignalR app basate su](xref:signalr/authn-and-authz) viene gestita quando viene stabilita la connessione. L'autenticazione può basarsi su un cookie o su altri bearer token.

Per ulteriori informazioni sulla creazione di app e configurazione, vedere <xref:security/blazor/server/index> .

## <a name="authenticationstateprovider-service"></a>Servizio AuthenticationStateProvider

Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> servizio incorporato ottiene i dati sullo stato di autenticazione da ASP.NET Core `HttpContext.User` . Questo è il modo in cui lo stato di autenticazione si integra con i meccanismi di autenticazione ASP.NET Core esistenti.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> è il servizio sottostante usato dal componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> e dal componente <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> per ottenere lo stato di autenticazione.

In genere non si usa <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> direttamente. Usare il [componente AuthorizeView](#authorizeview-component) o l' [attività \< AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) gli approcci descritti più avanti in questo articolo. Lo svantaggio principale dell'uso diretto di <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> è che il componente non riceve alcuna notifica automaticamente se i dati relativi allo stato di autenticazione sottostanti cambiano.

Il servizio <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> può fornire i dati <xref:System.Security.Claims.ClaimsPrincipal> dell'utente corrente, come illustrato nell'esempio seguente:

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

Se l'app richiede un provider personalizzato, implementare <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ed eseguire l'override di `GetAuthenticationStateAsync` :

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

In un' Blazor app webassembly il `CustomAuthStateProvider` servizio è registrato in `Main` di *Program.cs*:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

In un' Blazor app Server, il `CustomAuthStateProvider` servizio è registrato in `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Utilizzando l'oggetto `CustomAuthStateProvider` nell'esempio precedente, tutti gli utenti vengono autenticati con il nome utente `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Esporre lo stato di autenticazione come un parametro a catena

Se per la logica procedurale sono necessari dati sullo stato di autenticazione, ad esempio quando si esegue un'azione attivata dall'utente, ottenere i dati dello stato di autenticazione definendo un parametro di propagazione di tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

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

Configurare il parametro di propagazione `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` usando <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> i <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> componenti e nel `App` componente (*app. Razor*):

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

In un' Blazor app webassembly aggiungere servizi per le opzioni e l'autorizzazione a `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

In un' Blazor app Server i servizi per le opzioni e l'autorizzazione sono già presenti, quindi non sono necessarie ulteriori azioni.

## <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione di un utente, vengono applicate le regole di *autorizzazione* per controllare le operazioni consentite per un utente.

L'accesso viene in genere concesso o negato in base alle condizioni seguenti:

* L'utente è autenticato (ha eseguito l'accesso).
* L'utente è incluso in un *ruolo*.
* L'utente ha un'*attestazione*.
* I *criteri* sono soddisfatti.

Ognuno di questi concetti è identico a quello di un'app ASP.NET Core MVC o Razor pages. Per ulteriori informazioni sulla sicurezza ASP.NET Core, vedere gli articoli in [sicurezza ASP.NET Core e Identity ](xref:security/index).

## <a name="authorizeview-component"></a>Componente AuthorizeView

Il componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> visualizza in modo selettivo l'interfaccia utente a seconda del fatto che l'utente sia autorizzato a visualizzarla. Questo approccio è utile quando è necessario solo *visualizzare* dati per l'utente e non occorre usare l'identità dell'utente nella logica procedurale.

Il componente espone una variabile `context` di tipo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, che è possibile usare per accedere alle informazioni sull'utente connesso:

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

Il <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> componente può essere usato nel `NavMenu` componente (*Shared/NavMenu. Razor*) per visualizzare un elemento elenco ( `<li>...</li>` ) per un [componente NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), ma si noti che questo approccio rimuove solo l'elemento dell'elenco dall'output di cui è stato eseguito il rendering. Non impedisce all'utente di spostarsi nel componente.

Il contenuto dei `<Authorized>` `<NotAuthorized>` tag e può includere elementi arbitrari, ad esempio altri componenti interattivi.

Le condizioni di autorizzazione, ad esempio i ruoli o i criteri che consentono di controllare le opzioni dell'interfaccia utente o l'accesso, sono presentate nella sezione [Autorizzazione](#authorization).

Se non sono specificate condizioni di autorizzazione, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa criteri predefiniti e considera:

* Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.
* Gli utenti non autenticati (disconnessi) come non autorizzati.

### <a name="role-based-and-policy-based-authorization"></a>Autorizzazione basata sui ruoli e basata sui criteri

Il componente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> supporta l'autorizzazione *basata sui ruoli* oppure *basata sui criteri*.

Per l'autorizzazione basata sui ruoli, usare il parametro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Per altre informazioni, vedere <xref:security/authorization/roles>.

Per l'autorizzazione basata sui criteri, usare il parametro <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

L'autorizzazione basata sulle attestazioni è un caso speciale di autorizzazione basata su criteri. Ad esempio, è possibile definire un criterio che richiede che gli utenti abbiano una determinata attestazione. Per altre informazioni, vedere <xref:security/authorization/policies>.

Queste API possono essere usate nelle Blazor app Server o Blazor webassembly.

Se non si specifica <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> o <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> usa i criteri predefiniti.

### <a name="content-displayed-during-asynchronous-authentication"></a>Contenuto visualizzato durante l'autenticazione asincrona

Blazorconsente di determinare lo stato di autenticazione in *modo asincrono*. Lo scenario principale per questo approccio è nelle Blazor app webassembly che effettuano una richiesta a un endpoint esterno per l'autenticazione.

Mentre è in corso l'autenticazione <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> non visualizza alcun contenuto per impostazione predefinita. Per visualizzare contenuto durante l'autenticazione, usare l'elemento `<Authorizing>`:

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

Questo approccio non è in genere applicabile alle Blazor app Server. BlazorLe app server conoscono lo stato di autenticazione non appena viene stabilito lo stato. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>il contenuto può essere fornito nel Blazor componente di un'app Server <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , ma il contenuto non viene mai visualizzato.

## <a name="authorize-attribute"></a>Attributo [Authorize]

L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo può essere utilizzato nei Razor componenti di:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Usare solo [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sui `@page` componenti raggiunti tramite il Blazor router. L'autorizzazione viene eseguita solo come un aspetto del routing e *non* per i componenti figlio di cui viene eseguito il rendering all'interno di una pagina. Per autorizzare la visualizzazione di parti specifiche all'interno di una pagina, usare invece <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.

L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo supporta inoltre l'autorizzazione basata su ruoli o basata su criteri. Per l'autorizzazione basata sui ruoli, usare il parametro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Per l'autorizzazione basata sui criteri, usare il parametro <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Se non <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> si specifica né né, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Usa i criteri predefiniti, che per impostazione predefinita devono trattare:

* Gli utenti autenticati (che hanno eseguito l'accesso) come autorizzati.
* Gli utenti non autenticati (disconnessi) come non autorizzati.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Personalizzare il contenuto non autorizzato con il componente Router

Il <xref:Microsoft.AspNetCore.Components.Routing.Router> componente, insieme al <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> componente, consente all'app di specificare contenuto personalizzato se:

* Non viene trovato contenuto.
* L'utente non riesce [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) a applicare una condizione al componente. L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo viene analizzato nella sezione [ `[Authorize]` attribute](#authorize-attribute) .
* L'autenticazione asincrona è in corso.

Nel modello di Blazor progetto server predefinito, il `App` componente (*app. Razor*) illustra come impostare il contenuto personalizzato:

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

Il contenuto dei `<NotFound>` `<NotAuthorized>` tag, e `<Authorizing>` può includere elementi arbitrari, ad esempio altri componenti interattivi.

Se l' `<NotAuthorized>` elemento non è specificato, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> utilizza il seguente messaggio di fallback:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Notifica per le modifiche dello stato di autenticazione

Se l'app determina che i dati sullo stato di autenticazione sottostanti sono stati modificati, ad esempio perché l'utente disconnesso o un altro utente ha modificato i ruoli, un [AuthenticationStateProvider personalizzato](#implement-a-custom-authenticationstateprovider) può richiamare facoltativamente il metodo <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> sulla <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> classe di base. Viene così inviata notifica ai consumer dei dati di stato di autenticazione (ad esempio, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) di eseguire nuovamente il rendering usando i nuovi dati.

## <a name="procedural-logic"></a>Logica procedurale

Se l'app è necessaria per verificare le regole di autorizzazione come parte della logica procedurale, usare un parametro a cascata di tipo `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` per ottenere l'utente <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`può essere combinato con altri servizi, ad esempio `IAuthorizationService` , per valutare i criteri.

```razor
@using Microsoft.AspNetCore.Authorization
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
> In un Blazor componente dell'app webassembly aggiungere gli <xref:Microsoft.AspNetCore.Authorization> <xref:Microsoft.AspNetCore.Components.Authorization> spazi dei nomi e:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Questi spazi dei nomi possono essere forniti a livello globale aggiungendoli al file *_Imports. Razor* dell'app.

## <a name="authorization-in-blazor-webassembly-apps"></a>Autorizzazione nelle Blazor app Webassembly

Nelle Blazor app webassembly i controlli di autorizzazione possono essere ignorati perché tutto il codice lato client può essere modificato dagli utenti. Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.

**Eseguire sempre i controlli di autorizzazione nel server all'interno degli eventuali endpoint dell'API a cui accede l'app sul lato client.**

Per ulteriori informazioni, vedere gli articoli in <xref:security/blazor/webassembly/index> .

## <a name="troubleshoot-errors"></a>Risolvere gli errori

Errori comuni:

* **Per l'autorizzazione è necessario un parametro di propagazione di tipo Task \< AuthenticationState>. Per fornire questo, provare a usare CascadingAuthenticationState.**

* **`null`valore ricevuto per`authenticationStateTask`**

È probabile che il progetto non sia stato creato usando un Blazor modello server con autenticazione abilitata. Eseguire il wrapping di una `<CascadingAuthenticationState>` parte della struttura ad albero dell'interfaccia utente, ad esempio nel `App` componente (*app. Razor*) come indicato di seguito:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Fornisce il parametro di propagazione <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` , che a sua volta riceve dal <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> servizio di.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Awesome Blazor :](https://github.com/AdrienTorris/awesome-blazor#authentication) collegamenti di esempio della community di autenticazione
