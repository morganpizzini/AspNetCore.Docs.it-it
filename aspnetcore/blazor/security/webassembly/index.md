---
title: 'ASP.NET Core protetto Blazor WebAssembly'
author: guardrex
description: 'Informazioni su come proteggere Blazor le app WebAssemlby come applicazioni a pagina singola (Spa).'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/index
ms.openlocfilehash: fc6fa075faa4fed1c2bf938d82c4dbfe631c31d3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055067"
---
# <a name="secure-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="a0108-103">ASP.NET Core protetto Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="a0108-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="a0108-104">Di [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="a0108-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="a0108-105">Blazor WebAssembly le app sono protette in modo analogo alle applicazioni a pagina singola (Spa).</span><span class="sxs-lookup"><span data-stu-id="a0108-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="a0108-106">Esistono diversi approcci per l'autenticazione degli utenti in Spa, ma l'approccio più comune e completo consiste nell'usare un'implementazione basata sul [protocollo OAuth 2,0](https://oauth.net/), ad esempio [OPENID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="a0108-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="a0108-107">Libreria di autenticazione</span><span class="sxs-lookup"><span data-stu-id="a0108-107">Authentication library</span></span>

<span data-ttu-id="a0108-108">Blazor WebAssembly supporta l'autenticazione e l'autorizzazione delle app usando OIDC tramite la [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) libreria.</span><span class="sxs-lookup"><span data-stu-id="a0108-108">Blazor WebAssembly supports authenticating and authorizing apps using OIDC via the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library.</span></span> <span data-ttu-id="a0108-109">La libreria fornisce un set di primitive per l'autenticazione uniforme rispetto a ASP.NET Core backend.</span><span class="sxs-lookup"><span data-stu-id="a0108-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="a0108-110">La libreria si integra ASP.NET Core Identity con il supporto dell'autorizzazione API basato sul [ Identity server](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="a0108-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="a0108-111">La libreria può eseguire l'autenticazione per qualsiasi provider di terze parti Identity (IP) che supporta OIDC, chiamati provider OpenID (op).</span><span class="sxs-lookup"><span data-stu-id="a0108-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="a0108-112">Il supporto per l'autenticazione in Blazor WebAssembly è basato sulla `oidc-client.js` libreria, che consente di gestire i dettagli del protocollo di autenticazione sottostante.</span><span class="sxs-lookup"><span data-stu-id="a0108-112">The authentication support in Blazor WebAssembly is built on top of the `oidc-client.js` library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="a0108-113">Sono disponibili altre opzioni per l'autenticazione di Spa, ad esempio l'uso di navigava sullostesso sito cookie s.</span><span class="sxs-lookup"><span data-stu-id="a0108-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="a0108-114">Tuttavia, la progettazione di Blazor WebAssembly viene stabilita in OAuth e OIDC come opzione migliore per l'autenticazione nelle Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="a0108-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="a0108-115">È stata scelta l'autenticazione basata su [token](xref:security/anti-request-forgery#token-based-authentication) basata su [token Web JSON (token JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) per motivi funzionali e di sicurezza: [ cookie ](xref:security/anti-request-forgery#cookie-based-authentication)</span><span class="sxs-lookup"><span data-stu-id="a0108-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="a0108-116">L'uso di un protocollo basato su token offre una superficie di attacco più piccola, poiché i token non vengono inviati in tutte le richieste.</span><span class="sxs-lookup"><span data-stu-id="a0108-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="a0108-117">Gli endpoint server non richiedono la protezione da [richieste intersito falsificazione (CSRF)](xref:security/anti-request-forgery) perché i token vengono inviati in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="a0108-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="a0108-118">In questo modo è possibile ospitare Blazor WebAssembly app insieme a Razor app MVC o pagine.</span><span class="sxs-lookup"><span data-stu-id="a0108-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="a0108-119">I token hanno autorizzazioni più strette rispetto a cookie .</span><span class="sxs-lookup"><span data-stu-id="a0108-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="a0108-120">Ad esempio, non è possibile usare i token per gestire l'account utente o modificare la password di un utente, a meno che tale funzionalità non venga implementata in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="a0108-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="a0108-121">I token hanno una durata breve, un'ora per impostazione predefinita, che limita la finestra di attacco.</span><span class="sxs-lookup"><span data-stu-id="a0108-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="a0108-122">I token possono anche essere revocati in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="a0108-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="a0108-123">Il token JWT autonomo offre garanzie al client e al server per il processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="a0108-124">Un client, ad esempio, è in grado di rilevare e verificare che i token ricevuti siano legittimi e che siano stati emessi come parte di un determinato processo di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="a0108-125">Se una terza parte tenta di cambiare un token durante il processo di autenticazione, il client può rilevare il token cambiato ed evitare di usarlo.</span><span class="sxs-lookup"><span data-stu-id="a0108-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="a0108-126">I token con OAuth e OIDC non si basano sulla corretta presenza dell'agente utente per assicurarsi che l'applicazione sia protetta.</span><span class="sxs-lookup"><span data-stu-id="a0108-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="a0108-127">I protocolli basati su token, ad esempio OAuth e OIDC, consentono di autenticare e autorizzare app ospitate e autonome con lo stesso set di caratteristiche di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="a0108-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="a0108-128">Processo di autenticazione con OIDC</span><span class="sxs-lookup"><span data-stu-id="a0108-128">Authentication process with OIDC</span></span>

<span data-ttu-id="a0108-129">La [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) libreria offre diverse primitive per implementare l'autenticazione e l'autorizzazione usando OIDC.</span><span class="sxs-lookup"><span data-stu-id="a0108-129">The [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="a0108-130">In termini generali, l'autenticazione funziona nel modo seguente:</span><span class="sxs-lookup"><span data-stu-id="a0108-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="a0108-131">Quando un utente anonimo seleziona il pulsante di accesso o richiede una pagina con l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attributo applicato, l'utente viene reindirizzato alla pagina di accesso dell'app ( `/authentication/login` ).</span><span class="sxs-lookup"><span data-stu-id="a0108-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="a0108-132">Nella pagina di accesso, la libreria di autenticazione prepara un reindirizzamento all'endpoint di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="a0108-133">L'endpoint di autorizzazione è esterno all' Blazor WebAssembly app ed è possibile ospitarlo a un'origine separata.</span><span class="sxs-lookup"><span data-stu-id="a0108-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="a0108-134">L'endpoint è responsabile per determinare se l'utente è autenticato e per emettere uno o più token in risposta.</span><span class="sxs-lookup"><span data-stu-id="a0108-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="a0108-135">La libreria di autenticazione fornisce un callback di accesso per ricevere la risposta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="a0108-136">Se l'utente non è autenticato, l'utente viene reindirizzato al sistema di autenticazione sottostante, che è in genere ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a0108-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="a0108-137">Se l'utente è già stato autenticato, l'endpoint di autorizzazione genera i token appropriati e reindirizza di nuovo il browser all'endpoint di callback dell'account di accesso ( `/authentication/login-callback` ).</span><span class="sxs-lookup"><span data-stu-id="a0108-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="a0108-138">Quando l' Blazor WebAssembly app carica l'endpoint di callback dell'account di accesso ( `/authentication/login-callback` ), viene elaborata la risposta di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="a0108-139">Se il processo di autenticazione viene completato correttamente, l'utente viene autenticato e, facoltativamente, restituito all'URL protetto originale richiesto dall'utente.</span><span class="sxs-lookup"><span data-stu-id="a0108-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="a0108-140">Se il processo di autenticazione ha esito negativo per qualsiasi motivo, l'utente viene inviato alla pagina di accesso non riuscita ( `/authentication/login-failed` ) e viene visualizzato un errore.</span><span class="sxs-lookup"><span data-stu-id="a0108-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="authentication-component"></a><span data-ttu-id="a0108-141">Componente `Authentication`</span><span class="sxs-lookup"><span data-stu-id="a0108-141">`Authentication` component</span></span>

<span data-ttu-id="a0108-142">Il `Authentication` componente ( `Pages/Authentication.razor` ) gestisce le operazioni di autenticazione remota e consente all'app di:</span><span class="sxs-lookup"><span data-stu-id="a0108-142">The `Authentication` component (`Pages/Authentication.razor`) handles remote authentication operations and permits the app to:</span></span>

* <span data-ttu-id="a0108-143">Configurare le route delle app per gli Stati di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-143">Configure app routes for authentication states.</span></span>
* <span data-ttu-id="a0108-144">Imposta il contenuto dell'interfaccia utente per gli Stati di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-144">Set UI content for authentication states.</span></span>
* <span data-ttu-id="a0108-145">Gestire lo stato di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-145">Manage authentication state.</span></span>

<span data-ttu-id="a0108-146">Le azioni di autenticazione, ad esempio la registrazione o la firma in un utente, vengono passate al Blazor componente del Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> , che rende permanente e controlla lo stato tra le operazioni di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="a0108-146">Authentication actions, such as registering or signing in a user, are passed to the Blazor framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorViewCore%601> component, which persists and controls state across authentication operations.</span></span>

<span data-ttu-id="a0108-147">Per altre informazioni ed esempi, vedere <xref:blazor/security/webassembly/additional-scenarios>.</span><span class="sxs-lookup"><span data-stu-id="a0108-147">For more information and examples, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

## <a name="authorization"></a><span data-ttu-id="a0108-148">Autorizzazione</span><span class="sxs-lookup"><span data-stu-id="a0108-148">Authorization</span></span>

<span data-ttu-id="a0108-149">Nelle Blazor WebAssembly app è possibile ignorare i controlli di autorizzazione perché tutto il codice lato client può essere modificato dagli utenti.</span><span class="sxs-lookup"><span data-stu-id="a0108-149">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="a0108-150">Lo stesso vale per tutte le tecnologie per app sul lato client, tra cui i framework JavaScript SPA o le app native per qualsiasi sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="a0108-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="a0108-151">**Eseguire sempre i controlli di autorizzazione nel server all'interno degli eventuali endpoint dell'API a cui accede l'app sul lato client.**</span><span class="sxs-lookup"><span data-stu-id="a0108-151">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="require-authorization-for-the-entire-app"></a><span data-ttu-id="a0108-152">Richiedi l'autorizzazione per l'intera app</span><span class="sxs-lookup"><span data-stu-id="a0108-152">Require authorization for the entire app</span></span>

<span data-ttu-id="a0108-153">Applicare l' [ `[Authorize]` attributo](xref:blazor/security/index#authorize-attribute) ([documentazione API](xref:System.Web.Mvc.AuthorizeAttribute)) a ogni Razor componente dell'app usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0108-153">Apply the [`[Authorize]` attribute](xref:blazor/security/index#authorize-attribute) ([API documentation](xref:System.Web.Mvc.AuthorizeAttribute)) to each Razor component of the app using one of the following approaches:</span></span>

* <span data-ttu-id="a0108-154">Usare la [`@attribute`](xref:mvc/views/razor#attribute) direttiva nel `_Imports.razor` file:</span><span class="sxs-lookup"><span data-stu-id="a0108-154">Use the [`@attribute`](xref:mvc/views/razor#attribute) directive in the `_Imports.razor` file:</span></span>

  ```razor
  @using Microsoft.AspNetCore.Authorization
  @attribute [Authorize]
  ```

* <span data-ttu-id="a0108-155">Aggiungere l'attributo a ogni Razor componente nella `Pages` cartella.</span><span class="sxs-lookup"><span data-stu-id="a0108-155">Add the attribute to each Razor component in the `Pages` folder.</span></span>

> [!NOTE]
> <span data-ttu-id="a0108-156">L'impostazione <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> di un oggetto su un criterio con <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> **non** è supportata.</span><span class="sxs-lookup"><span data-stu-id="a0108-156">Setting an <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy?displayProperty=nameWithType> to a policy with <xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> is **not** supported.</span></span>

## <a name="refresh-tokens"></a><span data-ttu-id="a0108-157">Token di aggiornamento</span><span class="sxs-lookup"><span data-stu-id="a0108-157">Refresh tokens</span></span>

<span data-ttu-id="a0108-158">I token di aggiornamento non possono essere protetti sul lato client nelle Blazor WebAssembly app.</span><span class="sxs-lookup"><span data-stu-id="a0108-158">Refresh tokens can't be secured client-side in Blazor WebAssembly apps.</span></span> <span data-ttu-id="a0108-159">Pertanto, i token di aggiornamento non devono essere inviati all'app per l'uso diretto.</span><span class="sxs-lookup"><span data-stu-id="a0108-159">Therefore, refresh tokens shouldn't be sent to the app for direct use.</span></span>

<span data-ttu-id="a0108-160">I token di aggiornamento possono essere gestiti e usati dall'app sul lato server in una soluzione ospitata Blazor WebAssembly per accedere alle API di terze parti.</span><span class="sxs-lookup"><span data-stu-id="a0108-160">Refresh tokens can be maintained and used by the server-side app in a Hosted Blazor WebAssembly solution to access third-party APIs.</span></span> <span data-ttu-id="a0108-161">Per altre informazioni, vedere <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span><span class="sxs-lookup"><span data-stu-id="a0108-161">For more information, see <xref:blazor/security/webassembly/additional-scenarios#authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party>.</span></span>

## <a name="establish-claims-for-users"></a><span data-ttu-id="a0108-162">Stabilire attestazioni per gli utenti</span><span class="sxs-lookup"><span data-stu-id="a0108-162">Establish claims for users</span></span>

<span data-ttu-id="a0108-163">Le app richiedono spesso attestazioni per gli utenti in base a una chiamata API Web a un server.</span><span class="sxs-lookup"><span data-stu-id="a0108-163">Apps often require claims for users based on a web API call to a server.</span></span> <span data-ttu-id="a0108-164">Ad esempio, le attestazioni vengono spesso usate per [definire l'autorizzazione](xref:blazor/security/index#authorization) in un'app.</span><span class="sxs-lookup"><span data-stu-id="a0108-164">For example, claims are frequently used to [establish authorization](xref:blazor/security/index#authorization) in an app.</span></span> <span data-ttu-id="a0108-165">In questi scenari, l'app richiede un token di accesso per accedere al servizio e usa il token per ottenere i dati utente per le attestazioni.</span><span class="sxs-lookup"><span data-stu-id="a0108-165">In these scenarios, the app requests an access token to access the service and uses the token to obtain the user data for the claims.</span></span> <span data-ttu-id="a0108-166">Per esempi, vedere le risorse seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0108-166">For examples, see the following resources:</span></span>

* [<span data-ttu-id="a0108-167">Scenari aggiuntivi: personalizzare l'utente</span><span class="sxs-lookup"><span data-stu-id="a0108-167">Additional scenarios: Customize the user</span></span>](xref:blazor/security/webassembly/additional-scenarios#customize-the-user)
* <xref:blazor/security/webassembly/aad-groups-roles>

## <a name="implementation-guidance"></a><span data-ttu-id="a0108-168">Indicazioni relative all'implementazione</span><span class="sxs-lookup"><span data-stu-id="a0108-168">Implementation guidance</span></span>

<span data-ttu-id="a0108-169">Gli articoli in questa *Panoramica* forniscono informazioni sull'autenticazione degli utenti nelle Blazor WebAssembly app per provider specifici.</span><span class="sxs-lookup"><span data-stu-id="a0108-169">Articles under this *Overview* provide information on authenticating users in Blazor WebAssembly apps against specific providers.</span></span>

<span data-ttu-id="a0108-170">App autonome Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="a0108-170">Standalone Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="a0108-171">Linee guida generali per i provider OIDC e la libreria di autenticazione webassembly</span><span class="sxs-lookup"><span data-stu-id="a0108-171">General guidance for OIDC providers and the WebAssembly Authentication Library</span></span>](xref:blazor/security/webassembly/standalone-with-authentication-library)
* [<span data-ttu-id="a0108-172">Account Microsoft</span><span class="sxs-lookup"><span data-stu-id="a0108-172">Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="a0108-173">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="a0108-173">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="a0108-174">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="a0108-174">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory-b2c)

<span data-ttu-id="a0108-175">App ospitate Blazor WebAssembly :</span><span class="sxs-lookup"><span data-stu-id="a0108-175">Hosted Blazor WebAssembly apps:</span></span>

* [<span data-ttu-id="a0108-176">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="a0108-176">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
* [<span data-ttu-id="a0108-177">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="a0108-177">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
* [<span data-ttu-id="a0108-178">Identity Server</span><span class="sxs-lookup"><span data-stu-id="a0108-178">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a0108-179">Altre informazioni aggiuntive sulla configurazione sono disponibili negli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="a0108-179">Further configuration guidance is found in the following articles:</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* <xref:blazor/security/webassembly/graph-api>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a0108-180">Per ulteriori informazioni sulla configurazione, vedere <xref:blazor/security/webassembly/additional-scenarios> .</span><span class="sxs-lookup"><span data-stu-id="a0108-180">For further configuration guidance, see <xref:blazor/security/webassembly/additional-scenarios>.</span></span>

::: moniker-end
