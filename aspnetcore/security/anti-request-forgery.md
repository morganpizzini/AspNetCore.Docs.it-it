---
<span data-ttu-id="34c07-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-102">'Blazor'</span></span>
- <span data-ttu-id="34c07-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-103">'Identity'</span></span>
- <span data-ttu-id="34c07-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-105">'Razor'</span></span>
- <span data-ttu-id="34c07-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-106">'SignalR' uid:</span></span> 

---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="34c07-107">Impedisci gli attacchi di richiesta intersito falsa (XSRF/CSRF) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34c07-107">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="34c07-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan)e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="34c07-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="34c07-109">La richiesta tra siti falsificata (nota anche come XSRF o CSRF) è un attacco contro app ospitate sul Web, in cui un'app Web dannosa può influenzare l'interazione tra un browser client e un'app Web che considera attendibile il browser.</span><span class="sxs-lookup"><span data-stu-id="34c07-109">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="34c07-110">Questi attacchi sono possibili perché i Web browser inviano automaticamente alcuni tipi di token di autenticazione con ogni richiesta a un sito Web.</span><span class="sxs-lookup"><span data-stu-id="34c07-110">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="34c07-111">Questa forma di exploit è nota anche come un *attacco con un solo clic* o una *sessione di guida* perché l'attacco sfrutta la sessione precedentemente autenticata dell'utente.</span><span class="sxs-lookup"><span data-stu-id="34c07-111">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="34c07-112">Esempio di attacco CSRF:</span><span class="sxs-lookup"><span data-stu-id="34c07-112">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="34c07-113">Un utente accede `www.good-banking-site.com` utilizzando l'autenticazione basata su form.</span><span class="sxs-lookup"><span data-stu-id="34c07-113">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="34c07-114">Il server autentica l'utente e genera una risposta che include un cookie di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-114">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="34c07-115">Il sito è vulnerabile agli attacchi poiché considera attendibile qualsiasi richiesta ricevuta con un cookie di autenticazione valido.</span><span class="sxs-lookup"><span data-stu-id="34c07-115">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="34c07-116">L'utente visita un sito dannoso, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="34c07-116">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="34c07-117">Il sito dannoso, `www.bad-crook-site.com` , contiene un form HTML simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="34c07-117">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="34c07-118">Si noti che il post del modulo sul `action` sito vulnerabile, non sul sito dannoso.</span><span class="sxs-lookup"><span data-stu-id="34c07-118">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="34c07-119">Si tratta della parte "cross-site" di CSRF.</span><span class="sxs-lookup"><span data-stu-id="34c07-119">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="34c07-120">L'utente seleziona il pulsante Submit (Invia).</span><span class="sxs-lookup"><span data-stu-id="34c07-120">The user selects the submit button.</span></span> <span data-ttu-id="34c07-121">Il browser esegue la richiesta e include automaticamente il cookie di autenticazione per il dominio richiesto, `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="34c07-121">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="34c07-122">La richiesta viene eseguita sul `www.good-banking-site.com` server con il contesto di autenticazione dell'utente e può eseguire qualsiasi azione che un utente autenticato è autorizzato a eseguire.</span><span class="sxs-lookup"><span data-stu-id="34c07-122">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="34c07-123">Oltre allo scenario in cui l'utente seleziona il pulsante per l'invio del modulo, il sito dannoso potrebbe:</span><span class="sxs-lookup"><span data-stu-id="34c07-123">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="34c07-124">Eseguire uno script che invia automaticamente il modulo.</span><span class="sxs-lookup"><span data-stu-id="34c07-124">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="34c07-125">Inviare il modulo inviato come richiesta AJAX.</span><span class="sxs-lookup"><span data-stu-id="34c07-125">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="34c07-126">Nascondere il form usando CSS.</span><span class="sxs-lookup"><span data-stu-id="34c07-126">Hide the form using CSS.</span></span>

<span data-ttu-id="34c07-127">Questi scenari alternativi non richiedono alcuna azione o input da parte dell'utente, tranne che inizialmente visitare il sito dannoso.</span><span class="sxs-lookup"><span data-stu-id="34c07-127">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="34c07-128">L'uso di HTTPS non impedisce un attacco CSRF.</span><span class="sxs-lookup"><span data-stu-id="34c07-128">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="34c07-129">Il sito dannoso può inviare una richiesta con la stessa `https://www.good-banking-site.com/` facilità con cui è possibile inviare una richiesta non protetta.</span><span class="sxs-lookup"><span data-stu-id="34c07-129">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="34c07-130">Alcuni attacchi hanno come destinazione endpoint che rispondono alle richieste GET, nel qual caso è possibile usare un tag di immagine per eseguire l'azione.</span><span class="sxs-lookup"><span data-stu-id="34c07-130">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="34c07-131">Questo tipo di attacco è comune nei siti dei forum che consentono le immagini ma bloccano JavaScript.</span><span class="sxs-lookup"><span data-stu-id="34c07-131">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="34c07-132">Le app che modificano lo stato di richieste GET, in cui variabili o risorse vengono modificate, sono vulnerabili ad attacchi dannosi.</span><span class="sxs-lookup"><span data-stu-id="34c07-132">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="34c07-133">**Le richieste GET che cambiano lo stato non sono sicure. Una procedura consigliata consiste nel non modificare mai lo stato di una richiesta GET.**</span><span class="sxs-lookup"><span data-stu-id="34c07-133">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="34c07-134">Gli attacchi CSRF sono possibili per le app Web che usano i cookie per l'autenticazione perché:</span><span class="sxs-lookup"><span data-stu-id="34c07-134">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="34c07-135">I browser archiviano i cookie emessi da un'app Web.</span><span class="sxs-lookup"><span data-stu-id="34c07-135">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="34c07-136">I cookie archiviati includono i cookie di sessione per gli utenti autenticati.</span><span class="sxs-lookup"><span data-stu-id="34c07-136">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="34c07-137">I browser inviano tutti i cookie associati a un dominio all'app Web ogni richiesta, indipendentemente dalla modalità di generazione della richiesta all'app all'interno del browser.</span><span class="sxs-lookup"><span data-stu-id="34c07-137">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="34c07-138">Tuttavia, gli attacchi CSRF non sono limitati allo sfruttamento dei cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-138">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="34c07-139">Ad esempio, anche l'autenticazione di base e del digest è vulnerabile.</span><span class="sxs-lookup"><span data-stu-id="34c07-139">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="34c07-140">Quando un utente accede con l'autenticazione di base o del digest, il browser invia automaticamente le credenziali fino alla scadenza della sessione &dagger; .</span><span class="sxs-lookup"><span data-stu-id="34c07-140">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="34c07-141">&dagger;In questo contesto, *Session* fa riferimento alla sessione lato client durante la quale l'utente viene autenticato.</span><span class="sxs-lookup"><span data-stu-id="34c07-141">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="34c07-142">Non è correlato alle sessioni sul lato server o al [middleware della sessione ASP.NET Core](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="34c07-142">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="34c07-143">Gli utenti possono proteggersi da vulnerabilità CSRF adottando le precauzioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="34c07-143">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="34c07-144">Disconnettersi dalle app Web al termine dell'uso.</span><span class="sxs-lookup"><span data-stu-id="34c07-144">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="34c07-145">Cancellare periodicamente i cookie del browser.</span><span class="sxs-lookup"><span data-stu-id="34c07-145">Clear browser cookies periodically.</span></span>

<span data-ttu-id="34c07-146">Tuttavia, le vulnerabilità di CSRF sono fondamentalmente un problema con l'app Web e non con l'utente finale.</span><span class="sxs-lookup"><span data-stu-id="34c07-146">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="34c07-147">Nozioni fondamentali sull'autenticazione</span><span class="sxs-lookup"><span data-stu-id="34c07-147">Authentication fundamentals</span></span>

<span data-ttu-id="34c07-148">L'autenticazione basata su cookie è una forma comune di autenticazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-148">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="34c07-149">I sistemi di autenticazione basata su token hanno una maggiore popolarità, soprattutto per le applicazioni a pagina singola (Spa).</span><span class="sxs-lookup"><span data-stu-id="34c07-149">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="34c07-150">Autenticazione basata su cookie</span><span class="sxs-lookup"><span data-stu-id="34c07-150">Cookie-based authentication</span></span>

<span data-ttu-id="34c07-151">Quando un utente esegue l'autenticazione usando il nome utente e la password, viene emesso un token contenente un ticket di autenticazione che può essere usato per l'autenticazione e l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-151">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="34c07-152">Il token viene archiviato come cookie che accompagna ogni richiesta eseguita dal client.</span><span class="sxs-lookup"><span data-stu-id="34c07-152">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="34c07-153">La generazione e la convalida di questo cookie viene eseguita dal middleware di autenticazione dei cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-153">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="34c07-154">Il [middleware](xref:fundamentals/middleware/index) serializza un'entità utente in un cookie crittografato.</span><span class="sxs-lookup"><span data-stu-id="34c07-154">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="34c07-155">Nelle richieste successive il middleware convalida il cookie, ricrea l'entità e assegna l'entità alla proprietà [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) di [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="34c07-155">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="34c07-156">Autenticazione basata su token</span><span class="sxs-lookup"><span data-stu-id="34c07-156">Token-based authentication</span></span>

<span data-ttu-id="34c07-157">Quando un utente viene autenticato, viene emesso un token (non un token antifalsificazione).</span><span class="sxs-lookup"><span data-stu-id="34c07-157">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="34c07-158">Il token contiene informazioni sull'utente sotto forma di [attestazioni](/dotnet/framework/security/claims-based-identity-model) o un token di riferimento che punta l'app allo stato utente mantenuto nell'app.</span><span class="sxs-lookup"><span data-stu-id="34c07-158">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="34c07-159">Quando un utente tenta di accedere a una risorsa che richiede l'autenticazione, il token viene inviato all'app con un'intestazione di autorizzazione aggiuntiva nel formato token di connessione.</span><span class="sxs-lookup"><span data-stu-id="34c07-159">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="34c07-160">Questa operazione rende l'app senza stato.</span><span class="sxs-lookup"><span data-stu-id="34c07-160">This makes the app stateless.</span></span> <span data-ttu-id="34c07-161">In ogni richiesta successiva il token viene passato nella richiesta di convalida lato server.</span><span class="sxs-lookup"><span data-stu-id="34c07-161">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="34c07-162">Questo token non è *crittografato*; è *codificato*.</span><span class="sxs-lookup"><span data-stu-id="34c07-162">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="34c07-163">Sul server, il token viene decodificato per accedere alle informazioni.</span><span class="sxs-lookup"><span data-stu-id="34c07-163">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="34c07-164">Per inviare il token alle richieste successive, archiviare il token nella risorsa di archiviazione locale del browser.</span><span class="sxs-lookup"><span data-stu-id="34c07-164">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="34c07-165">Non preoccuparsi della vulnerabilità CSRF se il token è archiviato nella risorsa di archiviazione locale del browser.</span><span class="sxs-lookup"><span data-stu-id="34c07-165">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="34c07-166">CSRF rappresenta un problema quando il token viene archiviato in un cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-166">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="34c07-167">Per ulteriori informazioni, vedere l'esempio di codice di GitHub Issue [Spa aggiunge due cookie](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="34c07-167">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="34c07-168">Più app ospitate in un dominio</span><span class="sxs-lookup"><span data-stu-id="34c07-168">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="34c07-169">Gli ambienti di hosting condiviso sono vulnerabili al Hijack della sessione, all'account di accesso CSRF e ad altri attacchi.</span><span class="sxs-lookup"><span data-stu-id="34c07-169">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="34c07-170">Sebbene `example1.contoso.net` e `example2.contoso.net` siano host diversi, esiste una relazione di trust implicita tra gli host nel `*.contoso.net` dominio.</span><span class="sxs-lookup"><span data-stu-id="34c07-170">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="34c07-171">Questa relazione di trust implicita consente a host potenzialmente non attendibili di influenzare gli altri cookie (i criteri di origine identici che regolano le richieste AJAX non si applicano necessariamente ai cookie HTTP).</span><span class="sxs-lookup"><span data-stu-id="34c07-171">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="34c07-172">Gli attacchi che sfruttano i cookie attendibili tra le app ospitate nello stesso dominio possono essere impediti dalla condivisione dei domini.</span><span class="sxs-lookup"><span data-stu-id="34c07-172">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="34c07-173">Quando ogni app è ospitata nel proprio dominio, non esiste alcuna relazione di trust con i cookie implicita da sfruttare.</span><span class="sxs-lookup"><span data-stu-id="34c07-173">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="34c07-174">ASP.NET Core configurazione antifalsificazione</span><span class="sxs-lookup"><span data-stu-id="34c07-174">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="34c07-175">ASP.NET Core implementa la antifalsificazione usando la [protezione dei dati ASP.NET Core](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="34c07-175">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="34c07-176">Lo stack di protezione dei dati deve essere configurato in modo da funzionare in un server farm.</span><span class="sxs-lookup"><span data-stu-id="34c07-176">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="34c07-177">Per ulteriori informazioni, vedere [configurazione della protezione dei dati](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="34c07-177">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34c07-178">Il middleware antifalsificazione viene aggiunto al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) quando viene chiamata una delle API seguenti in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="34c07-178">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34c07-179">Il middleware antifalsificazione viene aggiunto al contenitore di [inserimento delle dipendenze](xref:fundamentals/dependency-injection) quando <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> viene chiamato in`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="34c07-179">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="34c07-180">In ASP.NET Core 2,0 o versioni successive, [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) inserisce i token antifalsificazione negli elementi del form HTML.</span><span class="sxs-lookup"><span data-stu-id="34c07-180">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="34c07-181">Il markup seguente in un Razor file genera automaticamente i token antifalsificazione:</span><span class="sxs-lookup"><span data-stu-id="34c07-181">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="34c07-182">Analogamente, [IHtmlHelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) genera token antifalsificazione per impostazione predefinita se il metodo del modulo non è Get.</span><span class="sxs-lookup"><span data-stu-id="34c07-182">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="34c07-183">La generazione automatica dei token antifalsificazione per gli elementi del form HTML si verifica quando il `<form>` tag contiene l' `method="post"` attributo e una delle condizioni seguenti è vera:</span><span class="sxs-lookup"><span data-stu-id="34c07-183">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="34c07-184">L'attributo Action è vuoto ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="34c07-184">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="34c07-185">L'attributo Action non è specificato ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="34c07-185">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="34c07-186">La generazione automatica dei token antifalsificazione per gli elementi del modulo HTML può essere disabilitata:</span><span class="sxs-lookup"><span data-stu-id="34c07-186">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="34c07-187">Disabilitare in modo esplicito i token antifalsificazione con l' `asp-antiforgery` attributo:</span><span class="sxs-lookup"><span data-stu-id="34c07-187">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="34c07-188">L'elemento form è escluso dagli Helper tag usando il simbolo Helper tag [! opt-out](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="34c07-188">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="34c07-189">Rimuovere `FormTagHelper` dalla visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-189">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="34c07-190">`FormTagHelper`È possibile rimuovere l'oggetto da una visualizzazione aggiungendo la direttiva seguente alla Razor vista:</span><span class="sxs-lookup"><span data-stu-id="34c07-190">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="34c07-191">Le [ Razor pagine](xref:razor-pages/index) vengono protette automaticamente da XSRF/CSRF.</span><span class="sxs-lookup"><span data-stu-id="34c07-191">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="34c07-192">Per ulteriori informazioni, vedere [XSRF/CSRF e Razor pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="34c07-192">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="34c07-193">L'approccio più comune alla difesa dagli attacchi CSRF consiste nell'usare il *modello di token di sincronizzazione* (STP).</span><span class="sxs-lookup"><span data-stu-id="34c07-193">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="34c07-194">STP viene usato quando l'utente richiede una pagina con dati del modulo:</span><span class="sxs-lookup"><span data-stu-id="34c07-194">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="34c07-195">Il server invia al client un token associato all'identità dell'utente corrente.</span><span class="sxs-lookup"><span data-stu-id="34c07-195">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="34c07-196">Il client invia il token al server per la verifica.</span><span class="sxs-lookup"><span data-stu-id="34c07-196">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="34c07-197">Se il server riceve un token che non corrisponde all'identità dell'utente autenticato, la richiesta viene rifiutata.</span><span class="sxs-lookup"><span data-stu-id="34c07-197">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="34c07-198">Il token è univoco e imprevedibile.</span><span class="sxs-lookup"><span data-stu-id="34c07-198">The token is unique and unpredictable.</span></span> <span data-ttu-id="34c07-199">Il token può essere usato anche per garantire la sequenziazione corretta di una serie di richieste, ad esempio per garantire la sequenza di richiesta: pagina 1 > pagina 2 > pagina 3).</span><span class="sxs-lookup"><span data-stu-id="34c07-199">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="34c07-200">Tutti i moduli in ASP.NET Core modelli MVC e di Razor pagine generano token antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-200">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="34c07-201">La coppia seguente di esempi di viste genera i token antifalsificazione:</span><span class="sxs-lookup"><span data-stu-id="34c07-201">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="34c07-202">Aggiungere in modo esplicito un token antifalsificazione a un `<form>` elemento senza usare gli helper tag con l'helper HTML [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="34c07-202">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="34c07-203">In ognuno dei casi precedenti, ASP.NET Core aggiunge un campo del form nascosto simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="34c07-203">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="34c07-204">ASP.NET Core include tre [filtri](xref:mvc/controllers/filters) per l'utilizzo di token antifalsificazione:</span><span class="sxs-lookup"><span data-stu-id="34c07-204">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="34c07-205">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="34c07-205">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="34c07-206">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="34c07-206">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="34c07-207">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="34c07-207">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="34c07-208">Opzioni antifalsificazione</span><span class="sxs-lookup"><span data-stu-id="34c07-208">Antiforgery options</span></span>

<span data-ttu-id="34c07-209">Personalizzare le [Opzioni di antifalsificazione](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="34c07-209">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="34c07-210">&dagger;Impostare le proprietà antifalsificazione `Cookie` usando le proprietà della classe [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) .</span><span class="sxs-lookup"><span data-stu-id="34c07-210">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="34c07-211">Opzione</span><span class="sxs-lookup"><span data-stu-id="34c07-211">Option</span></span> | <span data-ttu-id="34c07-212">Descrizione</span><span class="sxs-lookup"><span data-stu-id="34c07-212">Description</span></span> |
| ---
<span data-ttu-id="34c07-213">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-213">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-214">'Blazor'</span></span>
- <span data-ttu-id="34c07-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-215">'Identity'</span></span>
- <span data-ttu-id="34c07-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-217">'Razor'</span></span>
- <span data-ttu-id="34c07-218">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-218">'SignalR' uid:</span></span> 

<span data-ttu-id="34c07-219">--- | Titolo---: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-219">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-220">'Blazor'</span></span>
- <span data-ttu-id="34c07-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-221">'Identity'</span></span>
- <span data-ttu-id="34c07-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-223">'Razor'</span></span>
- <span data-ttu-id="34c07-224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="34c07-225">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-225">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-226">'Blazor'</span></span>
- <span data-ttu-id="34c07-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-227">'Identity'</span></span>
- <span data-ttu-id="34c07-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-229">'Razor'</span></span>
- <span data-ttu-id="34c07-230">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="34c07-231">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-231">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-232">'Blazor'</span></span>
- <span data-ttu-id="34c07-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-233">'Identity'</span></span>
- <span data-ttu-id="34c07-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-235">'Razor'</span></span>
- <span data-ttu-id="34c07-236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-236">'SignalR' uid:</span></span> 

<span data-ttu-id="34c07-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina le impostazioni utilizzate per creare i cookie antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-237">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="34c07-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nome del campo del form nascosto utilizzato dal sistema antifalsificazione per il rendering dei token antifalsificazione nelle viste.</span><span class="sxs-lookup"><span data-stu-id="34c07-238">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="34c07-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nome dell'intestazione utilizzata dal sistema antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-239">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="34c07-240">Se `null` , il sistema considera solo i dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="34c07-240">If `null`, the system considers only form data.</span></span> <span data-ttu-id="34c07-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifica se escludere la generazione dell' `X-Frame-Options` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-241">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="34c07-242">Per impostazione predefinita, l'intestazione viene generata con un valore "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="34c07-242">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="34c07-243">Il valore predefinito è `false`.</span><span class="sxs-lookup"><span data-stu-id="34c07-243">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="34c07-244">Opzione</span><span class="sxs-lookup"><span data-stu-id="34c07-244">Option</span></span> | <span data-ttu-id="34c07-245">Descrizione</span><span class="sxs-lookup"><span data-stu-id="34c07-245">Description</span></span> |
| ---
<span data-ttu-id="34c07-246">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-246">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-247">'Blazor'</span></span>
- <span data-ttu-id="34c07-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-248">'Identity'</span></span>
- <span data-ttu-id="34c07-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-250">'Razor'</span></span>
- <span data-ttu-id="34c07-251">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-251">'SignalR' uid:</span></span> 

<span data-ttu-id="34c07-252">--- | Titolo---: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-252">--- | --- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-253">'Blazor'</span></span>
- <span data-ttu-id="34c07-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-254">'Identity'</span></span>
- <span data-ttu-id="34c07-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-256">'Razor'</span></span>
- <span data-ttu-id="34c07-257">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="34c07-258">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-258">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-259">'Blazor'</span></span>
- <span data-ttu-id="34c07-260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-260">'Identity'</span></span>
- <span data-ttu-id="34c07-261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-261">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-262">'Razor'</span></span>
- <span data-ttu-id="34c07-263">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="34c07-264">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34c07-264">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34c07-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34c07-265">'Blazor'</span></span>
- <span data-ttu-id="34c07-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34c07-266">'Identity'</span></span>
- <span data-ttu-id="34c07-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34c07-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="34c07-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34c07-268">'Razor'</span></span>
- <span data-ttu-id="34c07-269">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34c07-269">'SignalR' uid:</span></span> 

<span data-ttu-id="34c07-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determina le impostazioni utilizzate per creare i cookie antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-270">------ | | [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Determines the settings used to create the antiforgery cookies.</span></span> <span data-ttu-id="34c07-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Dominio del cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-271">| | [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | The domain of the cookie.</span></span> <span data-ttu-id="34c07-272">Il valore predefinito è `null`.</span><span class="sxs-lookup"><span data-stu-id="34c07-272">Defaults to `null`.</span></span> <span data-ttu-id="34c07-273">Questa proprietà è obsoleta e verrà rimossa in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="34c07-273">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="34c07-274">L'alternativa consigliata è cookie. Domain.</span><span class="sxs-lookup"><span data-stu-id="34c07-274">The recommended alternative is Cookie.Domain.</span></span> <span data-ttu-id="34c07-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Nome del cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-275">| | [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | The name of the cookie.</span></span> <span data-ttu-id="34c07-276">Se non è impostato, il sistema genera un nome univoco che inizia con [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore. antifalsificazione. ").</span><span class="sxs-lookup"><span data-stu-id="34c07-276">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="34c07-277">Questa proprietà è obsoleta e verrà rimossa in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="34c07-277">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="34c07-278">L'alternativa consigliata è Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="34c07-278">The recommended alternative is Cookie.Name.</span></span> <span data-ttu-id="34c07-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Percorso impostato nel cookie.</span><span class="sxs-lookup"><span data-stu-id="34c07-279">| | [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | The path set on the cookie.</span></span> <span data-ttu-id="34c07-280">Questa proprietà è obsoleta e verrà rimossa in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="34c07-280">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="34c07-281">L'alternativa consigliata è cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="34c07-281">The recommended alternative is Cookie.Path.</span></span> <span data-ttu-id="34c07-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Nome del campo del form nascosto utilizzato dal sistema antifalsificazione per il rendering dei token antifalsificazione nelle viste.</span><span class="sxs-lookup"><span data-stu-id="34c07-282">| | [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> <span data-ttu-id="34c07-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Nome dell'intestazione utilizzata dal sistema antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-283">| | [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="34c07-284">Se `null` , il sistema considera solo i dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="34c07-284">If `null`, the system considers only form data.</span></span> <span data-ttu-id="34c07-285">| | [RequireSSL](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifica se HTTPS è richiesto dal sistema antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-285">| | [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="34c07-286">Se `true` , le richieste non HTTPS hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34c07-286">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="34c07-287">Il valore predefinito è `false`.</span><span class="sxs-lookup"><span data-stu-id="34c07-287">Defaults to `false`.</span></span> <span data-ttu-id="34c07-288">Questa proprietà è obsoleta e verrà rimossa in una versione futura.</span><span class="sxs-lookup"><span data-stu-id="34c07-288">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="34c07-289">L'alternativa consigliata consiste nell'impostare cookie. SecurePolicy.</span><span class="sxs-lookup"><span data-stu-id="34c07-289">The recommended alternative is to set Cookie.SecurePolicy.</span></span> <span data-ttu-id="34c07-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifica se escludere la generazione dell' `X-Frame-Options` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-290">| | [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="34c07-291">Per impostazione predefinita, l'intestazione viene generata con un valore "SAMEORIGIN".</span><span class="sxs-lookup"><span data-stu-id="34c07-291">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="34c07-292">Il valore predefinito è `false`.</span><span class="sxs-lookup"><span data-stu-id="34c07-292">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="34c07-293">Per ulteriori informazioni, vedere [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="34c07-293">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="34c07-294">Configurare le funzionalità antifalsificazione con IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="34c07-294">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="34c07-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) fornisce l'API per configurare le funzionalità antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-295">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="34c07-296">`IAntiforgery`può essere richiesto nel `Configure` metodo della `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="34c07-296">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="34c07-297">Nell'esempio seguente viene usato il middleware della home page dell'app per generare un token antifalsificazione e inviarlo nella risposta come cookie (usando la convenzione di denominazione angolare predefinita descritta più avanti in questo argomento):</span><span class="sxs-lookup"><span data-stu-id="34c07-297">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="34c07-298">Richiedi convalida antifalsificazione</span><span class="sxs-lookup"><span data-stu-id="34c07-298">Require antiforgery validation</span></span>

<span data-ttu-id="34c07-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) è un filtro azioni che può essere applicato a una singola azione, a un controller o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="34c07-299">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="34c07-300">Le richieste effettuate alle azioni con questo filtro vengono bloccate a meno che la richiesta non includa un token antifalsificazione valido.</span><span class="sxs-lookup"><span data-stu-id="34c07-300">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="34c07-301">L' `ValidateAntiForgeryToken` attributo richiede un token per le richieste ai metodi di azione contrassegnati, incluse le richieste HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="34c07-301">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="34c07-302">Se l' `ValidateAntiForgeryToken` attributo viene applicato tra i controller dell'app, è possibile eseguirne l'override con `IgnoreAntiforgeryToken` l'attributo.</span><span class="sxs-lookup"><span data-stu-id="34c07-302">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="34c07-303">ASP.NET Core non supporta l'aggiunta di token antifalsificazione per ottenere automaticamente le richieste.</span><span class="sxs-lookup"><span data-stu-id="34c07-303">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="34c07-304">Convalidare automaticamente i token antifalsificazione per metodi HTTP unsafe</span><span class="sxs-lookup"><span data-stu-id="34c07-304">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="34c07-305">ASP.NET Core app non generano token antifalsificazione per metodi HTTP sicuri (GET, HEAD, OPTIONS e TRACE).</span><span class="sxs-lookup"><span data-stu-id="34c07-305">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="34c07-306">Anziché applicare l' `ValidateAntiForgeryToken` attributo in generale e quindi eseguire l'override con `IgnoreAntiforgeryToken` gli attributi, è possibile usare l'attributo [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) .</span><span class="sxs-lookup"><span data-stu-id="34c07-306">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="34c07-307">Questo attributo funziona in modo identico all' `ValidateAntiForgeryToken` attributo, con la differenza che non richiede token per le richieste effettuate usando i metodi HTTP seguenti:</span><span class="sxs-lookup"><span data-stu-id="34c07-307">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="34c07-308">GET</span><span class="sxs-lookup"><span data-stu-id="34c07-308">GET</span></span>
* <span data-ttu-id="34c07-309">HEAD</span><span class="sxs-lookup"><span data-stu-id="34c07-309">HEAD</span></span>
* <span data-ttu-id="34c07-310">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="34c07-310">OPTIONS</span></span>
* <span data-ttu-id="34c07-311">TRACE</span><span class="sxs-lookup"><span data-stu-id="34c07-311">TRACE</span></span>

<span data-ttu-id="34c07-312">Si consiglia di usare `AutoValidateAntiforgeryToken` ampiamente per gli scenari non API.</span><span class="sxs-lookup"><span data-stu-id="34c07-312">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="34c07-313">Ciò garantisce che le azioni POST siano protette per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="34c07-313">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="34c07-314">In alternativa, è possibile ignorare i token antifalsificazione per impostazione predefinita, a meno che non `ValidateAntiForgeryToken` venga applicato a singoli metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="34c07-314">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="34c07-315">È più probabile che in questo scenario un metodo di azione POST venga lasciato non protetto per errore, lasciando l'app vulnerabile agli attacchi CSRF.</span><span class="sxs-lookup"><span data-stu-id="34c07-315">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="34c07-316">Tutti i post devono inviare il token antifalsificazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-316">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="34c07-317">Le API non hanno un meccanismo automatico per l'invio della parte non cookie del token.</span><span class="sxs-lookup"><span data-stu-id="34c07-317">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="34c07-318">È probabile che l'implementazione dipenda dall'implementazione del codice client.</span><span class="sxs-lookup"><span data-stu-id="34c07-318">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="34c07-319">Di seguito sono riportati alcuni esempi:</span><span class="sxs-lookup"><span data-stu-id="34c07-319">Some examples are shown below:</span></span>

<span data-ttu-id="34c07-320">Esempio a livello di classe:</span><span class="sxs-lookup"><span data-stu-id="34c07-320">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="34c07-321">Esempio globale:</span><span class="sxs-lookup"><span data-stu-id="34c07-321">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34c07-322">Servizi. AddMvc (opzioni = opzioni>. Filters. Add (New AutoValidateAntiforgeryTokenAttribute ()));</span><span class="sxs-lookup"><span data-stu-id="34c07-322">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="34c07-323">Sostituisci attributi antifalsificazione globali o controller</span><span class="sxs-lookup"><span data-stu-id="34c07-323">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="34c07-324">Il filtro [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) viene usato per eliminare la necessità di un token antifalsificazione per un'azione o un controller specifico.</span><span class="sxs-lookup"><span data-stu-id="34c07-324">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="34c07-325">Quando applicato, questo filtro sostituisce `ValidateAntiForgeryToken` i `AutoValidateAntiforgeryToken` filtri e specificati a un livello superiore (globalmente o in un controller).</span><span class="sxs-lookup"><span data-stu-id="34c07-325">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="34c07-326">Aggiornare i token dopo l'autenticazione</span><span class="sxs-lookup"><span data-stu-id="34c07-326">Refresh tokens after authentication</span></span>

<span data-ttu-id="34c07-327">I token devono essere aggiornati dopo che l'utente è stato autenticato reindirizzando l'utente a una pagina della visualizzazione o delle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="34c07-327">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="34c07-328">JavaScript, AJAX e Spa</span><span class="sxs-lookup"><span data-stu-id="34c07-328">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="34c07-329">Nelle app tradizionali basate su HTML, i token antifalsificazione vengono passati al server usando i campi dei moduli nascosti.</span><span class="sxs-lookup"><span data-stu-id="34c07-329">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="34c07-330">Nelle app moderne basate su JavaScript e in Spa, molte richieste vengono eseguite a livello di programmazione.</span><span class="sxs-lookup"><span data-stu-id="34c07-330">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="34c07-331">Queste richieste AJAX possono usare altre tecniche (ad esempio, intestazioni di richiesta o cookie) per inviare il token.</span><span class="sxs-lookup"><span data-stu-id="34c07-331">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="34c07-332">Se i cookie vengono usati per archiviare i token di autenticazione e per autenticare le richieste API sul server, CSRF è un potenziale problema.</span><span class="sxs-lookup"><span data-stu-id="34c07-332">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="34c07-333">Se viene usata l'archiviazione locale per archiviare il token, la vulnerabilità CSRF potrebbe essere attenuata perché i valori della risorsa di archiviazione locale non vengono inviati automaticamente al server con ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="34c07-333">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="34c07-334">Quindi, l'uso dell'archiviazione locale per archiviare il token antifalsificazione nel client e l'invio del token come intestazione della richiesta è un approccio consigliato.</span><span class="sxs-lookup"><span data-stu-id="34c07-334">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="34c07-335">JavaScript</span><span class="sxs-lookup"><span data-stu-id="34c07-335">JavaScript</span></span>

<span data-ttu-id="34c07-336">Utilizzando JavaScript con le visualizzazioni, è possibile creare il token utilizzando un servizio all'interno della vista.</span><span class="sxs-lookup"><span data-stu-id="34c07-336">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="34c07-337">Inserire il servizio [Microsoft. AspNetCore. antifalsification. IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) nella vista e chiamare [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="34c07-337">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="34c07-338">Questo approccio elimina la necessità di gestire direttamente l'impostazione dei cookie dal server o di leggerli dal client.</span><span class="sxs-lookup"><span data-stu-id="34c07-338">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="34c07-339">Nell'esempio precedente viene usato JavaScript per leggere il valore del campo nascosto per l'intestazione AJAX POST.</span><span class="sxs-lookup"><span data-stu-id="34c07-339">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="34c07-340">JavaScript può anche accedere ai token nei cookie e usare il contenuto del cookie per creare un'intestazione con il valore del token.</span><span class="sxs-lookup"><span data-stu-id="34c07-340">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="34c07-341">Supponendo che lo script richieda di inviare il token in un'intestazione denominata `X-CSRF-TOKEN` , configurare il servizio antifalsificazione per cercare l' `X-CSRF-TOKEN` intestazione:</span><span class="sxs-lookup"><span data-stu-id="34c07-341">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="34c07-342">Nell'esempio seguente viene usato JavaScript per creare una richiesta AJAX con l'intestazione appropriata:</span><span class="sxs-lookup"><span data-stu-id="34c07-342">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="34c07-343">AngularJS</span><span class="sxs-lookup"><span data-stu-id="34c07-343">AngularJS</span></span>

<span data-ttu-id="34c07-344">AngularJS usa una convenzione per indirizzare CSRF.</span><span class="sxs-lookup"><span data-stu-id="34c07-344">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="34c07-345">Se il server invia un cookie con il nome `XSRF-TOKEN` , il `$http` servizio AngularJS aggiunge il valore del cookie a un'intestazione quando invia una richiesta al server.</span><span class="sxs-lookup"><span data-stu-id="34c07-345">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="34c07-346">Questo processo è automatico.</span><span class="sxs-lookup"><span data-stu-id="34c07-346">This process is automatic.</span></span> <span data-ttu-id="34c07-347">L'intestazione non deve essere impostata in modo esplicito nel client.</span><span class="sxs-lookup"><span data-stu-id="34c07-347">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="34c07-348">Il nome dell'intestazione è `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="34c07-348">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="34c07-349">Il server deve rilevare questa intestazione e convalidarne il contenuto.</span><span class="sxs-lookup"><span data-stu-id="34c07-349">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="34c07-350">Per ASP.NET Core API da usare con questa convenzione nell'avvio dell'applicazione:</span><span class="sxs-lookup"><span data-stu-id="34c07-350">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="34c07-351">Configurare l'app per fornire un token in un cookie chiamato `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="34c07-351">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="34c07-352">Configurare il servizio antifalsificazione per cercare un'intestazione denominata `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="34c07-352">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="34c07-353">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34c07-353">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="34c07-354">Estendi antifalsificazione</span><span class="sxs-lookup"><span data-stu-id="34c07-354">Extend antiforgery</span></span>

<span data-ttu-id="34c07-355">Il tipo [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) consente agli sviluppatori di estendere il comportamento del sistema anti-CSRF eseguendo il round trip di dati aggiuntivi in ogni token.</span><span class="sxs-lookup"><span data-stu-id="34c07-355">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="34c07-356">Il metodo [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) viene chiamato ogni volta che viene generato un token di campo e il valore restituito viene incorporato all'interno del token generato.</span><span class="sxs-lookup"><span data-stu-id="34c07-356">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="34c07-357">Un responsabile dell'implementazione può restituire un timestamp, un parametro nonce o qualsiasi altro valore, quindi chiamare [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) per convalidare questi dati quando il token viene convalidato.</span><span class="sxs-lookup"><span data-stu-id="34c07-357">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="34c07-358">Il nome utente del client è già incorporato nei token generati, pertanto non è necessario includere tali informazioni.</span><span class="sxs-lookup"><span data-stu-id="34c07-358">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="34c07-359">Se un token include dati supplementari ma non `IAntiForgeryAdditionalDataProvider` è configurato alcun oggetto, i dati supplementari non vengono convalidati.</span><span class="sxs-lookup"><span data-stu-id="34c07-359">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34c07-360">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="34c07-360">Additional resources</span></span>

* <span data-ttu-id="34c07-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) in [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="34c07-361">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
