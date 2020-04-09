---
title: Proteggere ASP.NET Blazor Core WebAssembly
author: guardrex
description: Informazioni su Blazor come proteggere le app WebAssemlby come applicazioni a pagina singola (SPA).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: be286d770cd8d6e5cf7885b91be8654f74ffd743
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80538977"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Proteggere ASP.NET Blazor Core WebAssembly

Di [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorLe app WebAssembly sono protette nello stesso modo delle applicazioni a pagina singola (SPA, Single Page Applications). Esistono diversi approcci per l'autenticazione degli utenti alle tipo di dati PG, ma l'approccio più comune e completo consiste nell'utilizzare un'implementazione basata sul [protocollo OAuth 2.0](https://oauth.net/), ad esempio [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Libreria di autenticazione

BlazorWebAssembly supporta l'autenticazione e l'autorizzazione delle `Microsoft.AspNetCore.Components.WebAssembly.Authentication` app tramite OIDC tramite la libreria. La libreria fornisce un set di primitive per l'autenticazione trasparente rispetto ai back-end di ASP.NET Core.The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends. La libreria integra ASP.NET Core Identity con il supporto dell'autorizzazione API basato su [Identity Server](https://identityserver.io/). La libreria può eseguire l'autenticazione in base a qualsiasi provider di identità (IP) di terze parti che supporta OIDC, denominato provider OpenID (OP).

Il supporto Blazor dell'autenticazione in WebAssembly si basa sulla libreria *oidc-client.js,* utilizzata per gestire i dettagli del protocollo di autenticazione sottostante.

Esistono altre opzioni per l'autenticazione delle SPA, ad esempio l'uso di cookie SameSite. Tuttavia, la Blazor progettazione ingegneristica di WebAssembly viene stabilita su Blazor OAuth e OIDC come l'opzione migliore per l'autenticazione nelle app WebAssembly. [L'autenticazione basata](xref:security/anti-request-forgery#token-based-authentication) su token basato su token JSON Web Tokens (JWT) è stata scelta tramite [l'autenticazione basata su cookie](xref:security/anti-request-forgery#cookie-based-authentication) per motivi di funzionalità e sicurezza:Token-based authentication based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over cookie-based authentication for functional and security reasons:

* L'utilizzo di un protocollo basato su token offre una superficie di attacco più piccola, poiché i token non vengono inviati in tutte le richieste.
* Gli endpoint server non richiedono protezione contro la falsificazione di richieste [intersito (CSRF, Cross-Site Request Forgery)](xref:security/anti-request-forgery) perché i token vengono inviati in modo esplicito. In questo modo Blazor è possibile ospitare le applicazioni WebAssembly insieme alle app di pagine MVC o Razor.
* I token dispongono di autorizzazioni più ristrette rispetto ai cookie. Ad esempio, i token non possono essere utilizzati per gestire l'account utente o modificare la password di un utente, a meno che tale funzionalità non venga implementata in modo esplicito.
* I token hanno una breve durata, un'ora per impostazione predefinita, che limita la finestra di attacco. I token possono anche essere revocati in qualsiasi momento.
* I token JWT autonomi offrono garanzie al client e al server in merito al processo di autenticazione. Ad esempio, un client dispone dei mezzi per rilevare e convalidare che i token ricevuti sono legittimi e sono stati emessi come parte di un determinato processo di autenticazione. Se una terza parte tenta di cambiare un token nel corso del processo di autenticazione, il client può rilevare il token commutato ed evitare di usarlo.
* I token con OAuth e OIDC non si basano sul corretto funzionamento dell'agente utente per garantire la sicurezza dell'app.
* I protocolli basati su token, ad esempio OAuth e OIDC, consentono l'autenticazione e l'autorizzazione di app ospitate e autonome con lo stesso set di caratteristiche di sicurezza.

## <a name="authentication-process-with-oidc"></a>Processo di autenticazione con OIDC

La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` libreria offre diverse primitive per implementare l'autenticazione e l'autorizzazione utilizzando OIDC. In termini generali, l'autenticazione funziona come segue:

* Quando un utente anonimo seleziona il pulsante [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) di accesso o richiede una pagina con l'attributo applicato, viene reindirizzato alla pagina di accesso dell'app ( ).`/authentication/login`
* Nella pagina di accesso, la libreria di autenticazione prepara per un reindirizzamento all'endpoint di autorizzazione. L'endpoint di autorizzazione Blazor è esterno all'app WebAssembly e può essere ospitato in un'origine separata. L'endpoint è responsabile di determinare se l'utente è autenticato e per l'emissione di uno o più token in risposta. La libreria di autenticazione fornisce un callback di accesso per ricevere la risposta di autenticazione.
  * Se l'utente non è autenticato, viene reindirizzato al sistema di autenticazione sottostante, che in genere è ASP.NETCore Identity.If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.
  * Se l'utente è già stato autenticato, l'endpoint di autorizzazione genera i`/authentication/login-callback`token appropriati e reindirizza il browser all'endpoint di callback di accesso ( ).
* Quando Blazor l'app WebAssembly carica`/authentication/login-callback`l'endpoint di callback di accesso ( ), viene elaborata la risposta di autenticazione.
  * Se il processo di autenticazione viene completato correttamente, l'utente viene autenticato e, facoltativamente, inviato all'URL protetto originale richiesto dall'utente.
  * Se il processo di autenticazione non riesce per qualsiasi`/authentication/login-failed`motivo, l'utente viene inviato alla pagina di accesso non riuscito ( ) e viene visualizzato un errore.

## <a name="support-prerendering-with-authentication"></a>Supporta il prerendering con l'autenticazione

Dopo aver seguito le indicazioni Blazor contenute in uno degli argomenti dell'app WebAssembly ospitata, seguire le istruzioni seguenti per creare un'app che:

* Esegue il prerendering dei percorsi per i quali non è richiesta l'autorizzazione.
* Non esegue il prerendering dei percorsi per i quali è richiesta l'autorizzazione.

Nella classe dell'app `Program` client (*Program.cs*), fattorizzare le registrazioni dei servizi comuni in un metodo separato (ad esempio, `ConfigureCommonServices`):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        services.AddBaseAddressHttpClient();
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

Nell'app Server `Startup.ConfigureServices`registrare i seguenti servizi aggiuntivi:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

Nel metodo dell'app `Startup.Configure` Server, sostituire `endpoints.MapFallbackToFile("index.html")` con: `endpoints.MapFallbackToPage("/_Host")`

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Nell'app Server, crea una cartella *Pages* se non esiste. Creare una pagina *_Host.cshtml* all'interno della cartella *Pages* dell'app Server. Incolla il contenuto dal file *wwwroot/index.html* dell'app Client nel file *Pages/_Host.cshtml.* Aggiornare il contenuto del file:

* Aggiungere `@page "_Host"` all'inizio del file.
* Sostituire `<app>Loading...</app>` il tag con quanto segue:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opzioni per le app ospitate e i provider di accesso di terze parti

Quando si autentica e si Blazor autorizza un'app WebAssembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente. Quale si sceglie dipende dallo scenario.

Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Autenticare gli utenti per chiamare solo API di terze parti protetteAuthenticate users to only call protected third party APIs

Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:Authenticate the user with a client-side OAuth flow against the third-party API provider:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 In questo scenario:

* Il server che ospita l'app non ha un ruolo importante.
* Le API sul server non possono essere protette.
* L'app può chiamare solo API di terze parti protette.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Autenticare gli utenti con un provider di terze parti e chiamare API protette sul server host e sulla terza parte

Configurare l'identità con un provider di accesso di terze parti. Ottenere i token necessari per l'accesso alle API di terze parti e archiviarli.

Quando un utente esegue l'accesso, Identity raccoglie i token di accesso e aggiornamento come parte del processo di autenticazione. A questo punto, sono disponibili un paio di approcci per effettuare chiamate API a API di terze parti.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Usare un token di accesso al server per recuperare il token di accesso di terze partiUse a server access token to retrieve the third-party access token

Usare il token di accesso generato nel server per recuperare il token di accesso di terze parti da un endpoint API del server. A questo punto, utilizzare il token di accesso di terze parti per chiamare le risorse API di terze parti direttamente dall'identità sul client.

Non è consigliabile questo approccio. Questo approccio richiede il trattamento del token di accesso di terze parti come se fosse stato generato per un client pubblico. In termini OAuth, l'app pubblica non dispone di un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato. Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.

* Al token di accesso di terze parti potrebbero essere concessi ambiti aggiuntivi per eseguire operazioni riservate in base al fatto che la terza parte ha generato il token per un client più attendibile.
* Analogamente, i token di aggiornamento non devono essere rilasciati a un client non attendibile, in quanto in questo modo si ottiene l'accesso illimitato al client a meno che non vengano applicate altre restrizioni.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Effettuare chiamate API dal client all'API del server per chiamare API di terze parti

Effettuare una chiamata API dal client all'API del server. Dal server, recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.

Anche se questo approccio richiede un ulteriore hop di rete attraverso il server per chiamare un'API di terze parti, alla fine si traduce in un'esperienza più sicura:While this approach requires an extra network hop through the server to call a third-party API, it ultimate results in a safer experience:

* Il server può archiviare i token di aggiornamento e garantire che l'app non perda l'accesso alle risorse di terze parti.
* L'app non può perdere i token di accesso dal server che potrebbero contenere autorizzazioni più riservate.
