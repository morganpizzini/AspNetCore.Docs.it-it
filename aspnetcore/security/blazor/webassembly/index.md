---
title: Proteggere ASP.NET Blazor Core WebAssembly
author: guardrex
description: Informazioni su Blazor come proteggere le app WebAssemlby come applicazioni a pagina singola (SPA).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661787"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="9b172-103">Proteggere ASP.NET Blazor Core WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9b172-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="9b172-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="9b172-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Blazor<span data-ttu-id="9b172-105">Le app WebAssembly sono protette nello stesso modo delle applicazioni a pagina singola (SPA, Single Page Applications).</span><span class="sxs-lookup"><span data-stu-id="9b172-105"> WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="9b172-106">Esistono diversi approcci per l'autenticazione degli utenti alle tipo di dati PG, ma l'approccio più comune e completo consiste nell'utilizzare un'implementazione basata sul [protocollo OAuth 2.0](https://oauth.net/), ad esempio [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="9b172-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="9b172-107">Libreria di autenticazione</span><span class="sxs-lookup"><span data-stu-id="9b172-107">Authentication library</span></span>

Blazor<span data-ttu-id="9b172-108">WebAssembly supporta l'autenticazione e l'autorizzazione delle `Microsoft.AspNetCore.Components.WebAssembly.Authentication` app tramite OIDC tramite la libreria.</span><span class="sxs-lookup"><span data-stu-id="9b172-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="9b172-109">La libreria fornisce un set di primitive per l'autenticazione trasparente rispetto ai back-end di ASP.NET Core.The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span><span class="sxs-lookup"><span data-stu-id="9b172-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="9b172-110">La libreria integra ASP.NET Core Identity con il supporto dell'autorizzazione API basato su [Identity Server](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="9b172-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="9b172-111">La libreria può eseguire l'autenticazione in base a qualsiasi provider di identità (IP) di terze parti che supporta OIDC, denominato provider OpenID (OP).</span><span class="sxs-lookup"><span data-stu-id="9b172-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="9b172-112">Il supporto Blazor dell'autenticazione in WebAssembly si basa sulla libreria *oidc-client.js,* utilizzata per gestire i dettagli del protocollo di autenticazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="9b172-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="9b172-113">Esistono altre opzioni per l'autenticazione delle SPA, ad esempio l'uso di cookie SameSite.</span><span class="sxs-lookup"><span data-stu-id="9b172-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="9b172-114">Tuttavia, la Blazor progettazione ingegneristica di WebAssembly viene stabilita su Blazor OAuth e OIDC come l'opzione migliore per l'autenticazione nelle app WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="9b172-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="9b172-115">[L'autenticazione basata](xref:security/anti-request-forgery#token-based-authentication) su token basato su token JSON Web Tokens (JWT) è stata scelta tramite [l'autenticazione basata su cookie](xref:security/anti-request-forgery#cookie-based-authentication) per motivi di funzionalità e sicurezza:Token-based authentication based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over cookie-based authentication for functional and security reasons:</span><span class="sxs-lookup"><span data-stu-id="9b172-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="9b172-116">L'utilizzo di un protocollo basato su token offre una superficie di attacco più piccola, poiché i token non vengono inviati in tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="9b172-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="9b172-117">Gli endpoint server non richiedono protezione contro la falsificazione di richieste [intersito (CSRF, Cross-Site Request Forgery)](xref:security/anti-request-forgery) perché i token vengono inviati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="9b172-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="9b172-118">In questo modo Blazor è possibile ospitare le applicazioni WebAssembly insieme alle app di pagine MVC o Razor.</span><span class="sxs-lookup"><span data-stu-id="9b172-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="9b172-119">I token dispongono di autorizzazioni più ristrette rispetto ai cookie.</span><span class="sxs-lookup"><span data-stu-id="9b172-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="9b172-120">Ad esempio, i token non possono essere utilizzati per gestire l'account utente o modificare la password di un utente, a meno che tale funzionalità non venga implementata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="9b172-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="9b172-121">I token hanno una breve durata, un'ora per impostazione predefinita, che limita la finestra di attacco.</span><span class="sxs-lookup"><span data-stu-id="9b172-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="9b172-122">I token possono anche essere revocati in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="9b172-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="9b172-123">I token JWT autonomi offrono garanzie al client e al server in merito al processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="9b172-124">Ad esempio, un client dispone dei mezzi per rilevare e convalidare che i token ricevuti sono legittimi e sono stati emessi come parte di un determinato processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="9b172-125">Se una terza parte tenta di cambiare un token nel corso del processo di autenticazione, il client può rilevare il token commutato ed evitare di usarlo.</span><span class="sxs-lookup"><span data-stu-id="9b172-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="9b172-126">I token con OAuth e OIDC non si basano sul corretto funzionamento dell'agente utente per garantire la sicurezza dell'app.</span><span class="sxs-lookup"><span data-stu-id="9b172-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="9b172-127">I protocolli basati su token, ad esempio OAuth e OIDC, consentono l'autenticazione e l'autorizzazione di app ospitate e autonome con lo stesso set di caratteristiche di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="9b172-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="9b172-128">Processo di autenticazione con OIDC</span><span class="sxs-lookup"><span data-stu-id="9b172-128">Authentication process with OIDC</span></span>

<span data-ttu-id="9b172-129">La `Microsoft.AspNetCore.Components.WebAssembly.Authentication` libreria offre diverse primitive per implementare l'autenticazione e l'autorizzazione utilizzando OIDC.</span><span class="sxs-lookup"><span data-stu-id="9b172-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="9b172-130">In termini generali, l'autenticazione funziona come segue:</span><span class="sxs-lookup"><span data-stu-id="9b172-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="9b172-131">Quando un utente anonimo seleziona il pulsante [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) di accesso o richiede una pagina con l'attributo applicato, viene reindirizzato alla pagina di accesso dell'app ( ).`/authentication/login`</span><span class="sxs-lookup"><span data-stu-id="9b172-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="9b172-132">Nella pagina di accesso, la libreria di autenticazione prepara per un reindirizzamento all'endpoint di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="9b172-133">L'endpoint di autorizzazione Blazor è esterno all'app WebAssembly e può essere ospitato in un'origine separata.</span><span class="sxs-lookup"><span data-stu-id="9b172-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="9b172-134">L'endpoint è responsabile di determinare se l'utente è autenticato e per l'emissione di uno o più token in risposta.</span><span class="sxs-lookup"><span data-stu-id="9b172-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="9b172-135">La libreria di autenticazione fornisce un callback di accesso per ricevere la risposta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="9b172-136">Se l'utente non è autenticato, viene reindirizzato al sistema di autenticazione sottostante, che in genere è ASP.NETCore Identity.If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="9b172-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="9b172-137">Se l'utente è già stato autenticato, l'endpoint di autorizzazione genera i`/authentication/login-callback`token appropriati e reindirizza il browser all'endpoint di callback di accesso ( ).</span><span class="sxs-lookup"><span data-stu-id="9b172-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="9b172-138">Quando Blazor l'app WebAssembly carica`/authentication/login-callback`l'endpoint di callback di accesso ( ), viene elaborata la risposta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="9b172-139">Se il processo di autenticazione viene completato correttamente, l'utente viene autenticato e, facoltativamente, inviato all'URL protetto originale richiesto dall'utente.</span><span class="sxs-lookup"><span data-stu-id="9b172-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="9b172-140">Se il processo di autenticazione non riesce per qualsiasi`/authentication/login-failed`motivo, l'utente viene inviato alla pagina di accesso non riuscito ( ) e viene visualizzato un errore.</span><span class="sxs-lookup"><span data-stu-id="9b172-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="9b172-141">Supporta il prerendering con l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="9b172-141">Support prerendering with authentication</span></span>

<span data-ttu-id="9b172-142">Dopo aver seguito le indicazioni Blazor contenute in uno degli argomenti dell'app WebAssembly ospitata, seguire le istruzioni seguenti per creare un'app che:</span><span class="sxs-lookup"><span data-stu-id="9b172-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="9b172-143">Esegue il prerendering dei percorsi per i quali non è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="9b172-144">Non esegue il prerendering dei percorsi per i quali è richiesta l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="9b172-145">Nella classe dell'app `Program` client (*Program.cs*), fattorizzare le registrazioni dei servizi comuni in un metodo separato (ad esempio, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="9b172-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="9b172-146">Nell'app Server `Startup.ConfigureServices`registrare i seguenti servizi aggiuntivi:</span><span class="sxs-lookup"><span data-stu-id="9b172-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="9b172-147">Nel metodo dell'app `Startup.Configure` Server, sostituire `endpoints.MapFallbackToFile("index.html")` con: `endpoints.MapFallbackToPage("/_Host")`</span><span class="sxs-lookup"><span data-stu-id="9b172-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="9b172-148">Nell'app Server, crea una cartella *Pages* se non esiste.</span><span class="sxs-lookup"><span data-stu-id="9b172-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="9b172-149">Creare una pagina *_Host.cshtml* all'interno della cartella *Pages* dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="9b172-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="9b172-150">Incolla il contenuto dal file *wwwroot/index.html* dell'app Client nel file *Pages/_Host.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="9b172-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="9b172-151">Aggiornare il contenuto del file:</span><span class="sxs-lookup"><span data-stu-id="9b172-151">Update the file's contents:</span></span>

* <span data-ttu-id="9b172-152">Aggiungere `@page "_Host"` all'inizio del file.</span><span class="sxs-lookup"><span data-stu-id="9b172-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="9b172-153">Sostituire `<app>Loading...</app>` il tag con quanto segue:</span><span class="sxs-lookup"><span data-stu-id="9b172-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="9b172-154">Opzioni per le app ospitate e i provider di accesso di terze parti</span><span class="sxs-lookup"><span data-stu-id="9b172-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="9b172-155">Quando si autentica e si Blazor autorizza un'app WebAssembly ospitata con un provider di terze parti, sono disponibili diverse opzioni per l'autenticazione dell'utente.</span><span class="sxs-lookup"><span data-stu-id="9b172-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="9b172-156">Quale si sceglie dipende dallo scenario.</span><span class="sxs-lookup"><span data-stu-id="9b172-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="9b172-157">Per altre informazioni, vedere <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="9b172-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="9b172-158">Autenticare gli utenti per chiamare solo API di terze parti protetteAuthenticate users to only call protected third party APIs</span><span class="sxs-lookup"><span data-stu-id="9b172-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="9b172-159">Autenticare l'utente con un flusso OAuth sul lato client rispetto al provider di API di terze parti:Authenticate the user with a client-side OAuth flow against the third-party API provider:</span><span class="sxs-lookup"><span data-stu-id="9b172-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="9b172-160">In questo scenario:</span><span class="sxs-lookup"><span data-stu-id="9b172-160">In this scenario:</span></span>

* <span data-ttu-id="9b172-161">Il server che ospita l'app non ha un ruolo importante.</span><span class="sxs-lookup"><span data-stu-id="9b172-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="9b172-162">Le API sul server non possono essere protette.</span><span class="sxs-lookup"><span data-stu-id="9b172-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="9b172-163">L'app può chiamare solo API di terze parti protette.</span><span class="sxs-lookup"><span data-stu-id="9b172-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="9b172-164">Autenticare gli utenti con un provider di terze parti e chiamare API protette sul server host e sulla terza parte</span><span class="sxs-lookup"><span data-stu-id="9b172-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="9b172-165">Configurare l'identità con un provider di accesso di terze parti.</span><span class="sxs-lookup"><span data-stu-id="9b172-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="9b172-166">Ottenere i token necessari per l'accesso alle API di terze parti e archiviarli.</span><span class="sxs-lookup"><span data-stu-id="9b172-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="9b172-167">Quando un utente esegue l'accesso, Identity raccoglie i token di accesso e aggiornamento come parte del processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="9b172-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="9b172-168">A questo punto, sono disponibili un paio di approcci per effettuare chiamate API a API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="9b172-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="9b172-169">Usare un token di accesso al server per recuperare il token di accesso di terze partiUse a server access token to retrieve the third-party access token</span><span class="sxs-lookup"><span data-stu-id="9b172-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="9b172-170">Usare il token di accesso generato nel server per recuperare il token di accesso di terze parti da un endpoint API del server.</span><span class="sxs-lookup"><span data-stu-id="9b172-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="9b172-171">A questo punto, utilizzare il token di accesso di terze parti per chiamare le risorse API di terze parti direttamente dall'identità sul client.</span><span class="sxs-lookup"><span data-stu-id="9b172-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="9b172-172">Non è consigliabile questo approccio.</span><span class="sxs-lookup"><span data-stu-id="9b172-172">We don't recommend this approach.</span></span> <span data-ttu-id="9b172-173">Questo approccio richiede il trattamento del token di accesso di terze parti come se fosse stato generato per un client pubblico.</span><span class="sxs-lookup"><span data-stu-id="9b172-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="9b172-174">In termini OAuth, l'app pubblica non dispone di un segreto client perché non può essere considerata attendibile per archiviare i segreti in modo sicuro e il token di accesso viene prodotto per un client riservato.</span><span class="sxs-lookup"><span data-stu-id="9b172-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="9b172-175">Un client riservato è un client che dispone di un segreto client e si presuppone che sia in grado di archiviare in modo sicuro i segreti.</span><span class="sxs-lookup"><span data-stu-id="9b172-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="9b172-176">Al token di accesso di terze parti potrebbero essere concessi ambiti aggiuntivi per eseguire operazioni riservate in base al fatto che la terza parte ha generato il token per un client più attendibile.</span><span class="sxs-lookup"><span data-stu-id="9b172-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="9b172-177">Analogamente, i token di aggiornamento non devono essere rilasciati a un client non attendibile, in quanto in questo modo si ottiene l'accesso illimitato al client a meno che non vengano applicate altre restrizioni.</span><span class="sxs-lookup"><span data-stu-id="9b172-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="9b172-178">Effettuare chiamate API dal client all'API del server per chiamare API di terze parti</span><span class="sxs-lookup"><span data-stu-id="9b172-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="9b172-179">Effettuare una chiamata API dal client all'API del server.</span><span class="sxs-lookup"><span data-stu-id="9b172-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="9b172-180">Dal server, recuperare il token di accesso per la risorsa API di terze parti ed emettere qualsiasi chiamata necessaria.</span><span class="sxs-lookup"><span data-stu-id="9b172-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="9b172-181">Anche se questo approccio richiede un ulteriore hop di rete attraverso il server per chiamare un'API di terze parti, alla fine si traduce in un'esperienza più sicura:While this approach requires an extra network hop through the server to call a third-party API, it ultimate results in a safer experience:</span><span class="sxs-lookup"><span data-stu-id="9b172-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="9b172-182">Il server può archiviare i token di aggiornamento e garantire che l'app non perda l'accesso alle risorse di terze parti.</span><span class="sxs-lookup"><span data-stu-id="9b172-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="9b172-183">L'app non può perdere i token di accesso dal server che potrebbero contenere autorizzazioni più riservate.</span><span class="sxs-lookup"><span data-stu-id="9b172-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
