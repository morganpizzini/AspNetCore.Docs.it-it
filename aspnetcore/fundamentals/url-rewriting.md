---
<span data-ttu-id="72750-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-102">'Blazor'</span></span>
- <span data-ttu-id="72750-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-103">'Identity'</span></span>
- <span data-ttu-id="72750-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-105">'Razor'</span></span>
- <span data-ttu-id="72750-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="72750-107">Middleware Riscrittura URL in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="72750-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="72750-108">Di [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="72750-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="72750-109">Questo documento presenta la riscrittura URL con istruzioni per l'uso del middleware Riscrittura URL nelle applicazioni ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72750-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="72750-110">La riscrittura URL è l'azione di modificare gli URL di richiesta in base a una o più regole predefinite.</span><span class="sxs-lookup"><span data-stu-id="72750-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="72750-111">Il processo di riscrittura URL crea un'astrazione tra i percorsi delle risorse e i relativi indirizzi, in modo che i percorsi e gli indirizzi non risultino strettamente collegati.</span><span class="sxs-lookup"><span data-stu-id="72750-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="72750-112">La riscrittura URL risulta utile in diversi scenari per:</span><span class="sxs-lookup"><span data-stu-id="72750-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="72750-113">Spostare o sostituire in modo temporaneo o permanente risorse server mantenendo localizzatori stabili di queste risorse.</span><span class="sxs-lookup"><span data-stu-id="72750-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="72750-114">Suddividere l'elaborazione delle richieste tra app diverse o tra aree diverse di un'unica app.</span><span class="sxs-lookup"><span data-stu-id="72750-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="72750-115">Rimuovere, aggiungere o riorganizzare segmenti URL nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="72750-116">Ottimizzare gli URL pubblici per l'ottimizzazione motore di ricerca (SEO, Search Engine Optimization).</span><span class="sxs-lookup"><span data-stu-id="72750-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="72750-117">Consentire l'uso di URL pubblici descrittivi in modo che i visitatori possano prevedere il contenuto restituito dalla richiesta di una risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="72750-118">Reindirizzare le richieste non protette a endpoint protetti.</span><span class="sxs-lookup"><span data-stu-id="72750-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="72750-119">Evitare l'hotlinking, ovvero l'uso da parte di un sito esterno di una risorsa statica ospitata presente in un altro sito tramite il collegamento di questa al proprio contenuto.</span><span class="sxs-lookup"><span data-stu-id="72750-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="72750-120">La riscrittura URL può ridurre le prestazioni di un'app.</span><span class="sxs-lookup"><span data-stu-id="72750-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="72750-121">Ove possibile, limitare il numero e la complessità delle regole.</span><span class="sxs-lookup"><span data-stu-id="72750-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="72750-122">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72750-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="72750-123">Reindirizzamento URL e riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="72750-124">La differenza tra i termini *reindirizzamento URL* e *riscrittura URL* è minima, ma ha implicazioni importanti nell'offerta di risorse ai clienti.</span><span class="sxs-lookup"><span data-stu-id="72750-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="72750-125">Il middleware Riscrittura URL di ASP.NET Core è in grado di svolgere entrambe le funzioni.</span><span class="sxs-lookup"><span data-stu-id="72750-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="72750-126">Un *reindirizzamento URL* implica un'operazione lato client, in cui viene richiesto al client di accedere a una risorsa a un indirizzo diverso da quello richiesto originariamente dal client.</span><span class="sxs-lookup"><span data-stu-id="72750-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="72750-127">Questa operazione richiede un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-127">This requires a round trip to the server.</span></span> <span data-ttu-id="72750-128">L'URL di reindirizzamento restituito al client viene visualizzato nella barra degli indirizzi del browser quando il client effettua una nuova richiesta per la risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="72750-129">In caso di *reindirizzamento* di `/resource` a `/different-resource`, il server risponde che il client deve ottenere la risorsa presso `/different-resource` con un codice di stato che indica se il reindirizzamento è temporaneo o permanente.</span><span class="sxs-lookup"><span data-stu-id="72750-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Un endpoint di servizio WebAPI è stato modificato temporaneamente dalla versione 1 (v1) alla versione 2 (v2) nel server.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="72750-135">Quando si reindirizzano le richieste a un URL diverso, indicare se il reindirizzamento è temporaneo o permanente specificando il codice di stato con la risposta:</span><span class="sxs-lookup"><span data-stu-id="72750-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="72750-136">Il codice di stato *301 - Spostato permanentemente* viene usato se la risorsa ha un nuovo URL definitivo e si vuole indicare al client che tale URL dovrà essere usato per tutte le richieste future della risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="72750-137">*Quando riceve un codice di stato 301, il client può memorizzare la risposta nella cache e riusarla.*</span><span class="sxs-lookup"><span data-stu-id="72750-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="72750-138">Il codice di stato *302 - Trovato* viene usato quando il reindirizzamento è temporaneo o comunque soggetto a modifiche.</span><span class="sxs-lookup"><span data-stu-id="72750-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="72750-139">Il codice di stato 302 indica che il client non dovrà archiviare e riusare l'URL di reindirizzamento in futuro.</span><span class="sxs-lookup"><span data-stu-id="72750-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="72750-140">Per altre informazioni sui codici di stato, vedere [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: Definizioni dei codici di stato).</span><span class="sxs-lookup"><span data-stu-id="72750-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="72750-141">La *riscrittura URL* è un'operazione lato server che rende disponibile una risorsa da un indirizzo diverso da quello richiesto dal client.</span><span class="sxs-lookup"><span data-stu-id="72750-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="72750-142">La riscrittura URL non richiede un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="72750-143">L'URL riscritto non viene restituito al client e non viene visualizzato nella barra degli indirizzi del browser.</span><span class="sxs-lookup"><span data-stu-id="72750-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="72750-144">Se `/resource` viene *riscritto* in `/different-resource`, il server recupera *internamente* la risorsa in `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="72750-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="72750-145">Anche se il client fosse in grado di recuperare la risorsa nell'URL riscritto, non viene informato dell'esistenza della risorsa nell'URL riscritto quando effettua la richiesta e riceve la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Un endpoint servizio WebAPI è stato modificato dalla versione 1 (v1) alla versione 2 (v2) sul server.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="72750-150">App di esempio per la riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-150">URL rewriting sample app</span></span>

<span data-ttu-id="72750-151">È possibile esplorare le funzionalità del middleware Riscrittura URL con l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="72750-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="72750-152">L'app applica regole di reindirizzamento e riscrittura e visualizza l'URL reindirizzato o riscritto per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="72750-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="72750-153">Quando usare il middleware Riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="72750-154">Usare il middleware Riscrittura URL quando non è possibile usare gli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="72750-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="72750-155">URL Rewrite Module for IIS in Windows Server</span><span class="sxs-lookup"><span data-stu-id="72750-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="72750-156">Modulo Apache mod_rewrite in Apache Server</span><span class="sxs-lookup"><span data-stu-id="72750-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="72750-157">Riscrittura URL in Nginx</span><span class="sxs-lookup"><span data-stu-id="72750-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="72750-158">Usare il middleware anche se l'app è ospitata in un [server HTTP.sys](xref:fundamentals/servers/httpsys) (in precedenza denominato WebListener).</span><span class="sxs-lookup"><span data-stu-id="72750-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="72750-159">I motivi principali per usare tecnologie di riscrittura URL basate su server in IIS, Apache e Nginx sono:</span><span class="sxs-lookup"><span data-stu-id="72750-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="72750-160">Il middleware non supporta tutte le funzionalità di questi moduli.</span><span class="sxs-lookup"><span data-stu-id="72750-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="72750-161">Alcune funzionalità dei moduli server non funzionano con i progetti ASP.NET Core, ad esempio i vincoli `IsFile` e `IsDirectory` del modulo IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="72750-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="72750-162">In questi scenari è necessario usare il middleware.</span><span class="sxs-lookup"><span data-stu-id="72750-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="72750-163">Le prestazioni del middleware probabilmente non corrispondono a quelle dei moduli.</span><span class="sxs-lookup"><span data-stu-id="72750-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="72750-164">Il benchmarking rappresenta l'unico modo per sapere con certezza quale approccio comporta la maggior riduzione delle prestazioni o se tale riduzione è trascurabile.</span><span class="sxs-lookup"><span data-stu-id="72750-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="72750-165">Pacchetto</span><span class="sxs-lookup"><span data-stu-id="72750-165">Package</span></span>

<span data-ttu-id="72750-166">Il middleware di riscrittura URL è fornito dal pacchetto [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), implicitamente incluso nelle app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72750-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="72750-167">Estensioni e opzioni</span><span class="sxs-lookup"><span data-stu-id="72750-167">Extension and options</span></span>

<span data-ttu-id="72750-168">È possibile definire regole di riscrittura e reindirizzamento URL creando un'istanza della classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con metodi di estensione per le singole regole di riscrittura.</span><span class="sxs-lookup"><span data-stu-id="72750-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="72750-169">Concatenare più regole nell'ordine in cui si vuole che vengano elaborate.</span><span class="sxs-lookup"><span data-stu-id="72750-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="72750-170">Le opzioni `RewriteOptions` vengono passate al middleware Riscrittura URL quando questo viene aggiunto alla pipeline della richiesta con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="72750-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="72750-171">Reindirizzare non-www su www</span><span class="sxs-lookup"><span data-stu-id="72750-171">Redirect non-www to www</span></span>

<span data-ttu-id="72750-172">Sono disponibili tre opzioni che consentono all'app di reindirizzare richieste non-`www` su `www`:</span><span class="sxs-lookup"><span data-stu-id="72750-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="72750-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Reindirizza in modo permanente la richiesta al `www` sottodominio se la richiesta è non `www` .</span><span class="sxs-lookup"><span data-stu-id="72750-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="72750-174">Reindirizza con un codice di stato [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="72750-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="72750-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Reindirizza la richiesta al `www` sottodominio se la richiesta in ingresso è non `www` .</span><span class="sxs-lookup"><span data-stu-id="72750-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="72750-176">Reindirizza con un codice di stato [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="72750-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="72750-177">Un overload consente di specificare il codice di stato per la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="72750-178">Usare un campo della classe <xref:Microsoft.AspNetCore.Http.StatusCodes> per un'assegnazione di codice di stato.</span><span class="sxs-lookup"><span data-stu-id="72750-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="72750-179">Reindirizzamento URL</span><span class="sxs-lookup"><span data-stu-id="72750-179">URL redirect</span></span>

<span data-ttu-id="72750-180">Per reindirizzare le richieste, usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>.</span><span class="sxs-lookup"><span data-stu-id="72750-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="72750-181">Il primo parametro contiene l'espressione regolare per la corrispondenza in base al percorso dell'URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="72750-182">Il secondo parametro è la stringa di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="72750-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="72750-183">Il terzo parametro, se presente, specifica il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="72750-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="72750-184">Se il codice di stato non è specificato, assume come valore predefinito *302 - Trovato*, che indica che la risorsa è stata temporaneamente spostata o sostituita.</span><span class="sxs-lookup"><span data-stu-id="72750-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="72750-185">In un browser con gli strumenti di sviluppo abilitati, creare una richiesta all'app di esempio con il percorso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="72750-186">L'espressione regolare definisce la corrispondenza con il percorso di richiesta in `redirect-rule/(.*)` e il percorso viene sostituito con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="72750-187">L'URL di reindirizzamento viene restituito al client con il codice di stato *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="72750-188">Il browser invia una nuova richiesta all'URL di reindirizzamento, che viene visualizzata nella barra degli indirizzi del browser.</span><span class="sxs-lookup"><span data-stu-id="72750-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="72750-189">Poiché nessuna regola nell'app di esempio corrisponde all'URL di reindirizzamento:</span><span class="sxs-lookup"><span data-stu-id="72750-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="72750-190">La seconda richiesta riceve una risposta *200 - OK* dall'app.</span><span class="sxs-lookup"><span data-stu-id="72750-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="72750-191">Il corpo della risposta visualizza l'URL di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="72750-192">Quando un URL viene *reindirizzato*, viene eseguito un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="72750-193">Prestare attenzione quando si definiscono regole di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="72750-194">Le regole di reindirizzamento vengono valutate in ogni richiesta all'app, anche dopo un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="72750-195">È facile creare inavvertitamente un *ciclo di reindirizzamento infinito*.</span><span class="sxs-lookup"><span data-stu-id="72750-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="72750-196">Richiesta originale: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="72750-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="72750-198">La parte dell'espressione racchiusa tra parentesi è denominata *gruppo Capture*.</span><span class="sxs-lookup"><span data-stu-id="72750-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="72750-199">Il punto (`.`) dell'espressione significa *corrispondenza con qualsiasi carattere*.</span><span class="sxs-lookup"><span data-stu-id="72750-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="72750-200">L'asterisco (`*`) indica *corrispondenza con il carattere precedente per zero o più volte*.</span><span class="sxs-lookup"><span data-stu-id="72750-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="72750-201">Di conseguenza gli ultimi due i segmenti di percorso dell'URL `1234/5678` vengono acquisiti tramite il gruppo Capture `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="72750-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="72750-202">Qualsiasi valore visualizzato nell'URL della richiesta dopo `redirect-rule/` viene acquisito da questo gruppo Capture.</span><span class="sxs-lookup"><span data-stu-id="72750-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="72750-203">Nella stringa di sostituzione i gruppi acquisiti vengono inseriti nella stringa con il simbolo di dollaro (`$`) seguito dal numero di sequenza dell'acquisizione.</span><span class="sxs-lookup"><span data-stu-id="72750-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="72750-204">Il primo valore del gruppo Capture viene ottenuto con `$1`, il secondo con `$2` e la procedura continua in sequenza per i gruppi Capture presenti nell'espressione regolare.</span><span class="sxs-lookup"><span data-stu-id="72750-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="72750-205">Nell'espressione regolare della regola di reindirizzamento dell'app di esempio è presente un solo gruppo acquisito, pertanto nella stringa di sostituzione è presente un solo gruppo inserito, ovvero `$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="72750-206">Quando viene applicata la regola, l'URL diventa `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="72750-207">Reindirizzamento URL a un endpoint protetto</span><span class="sxs-lookup"><span data-stu-id="72750-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="72750-208">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> per reindirizzare le richieste HTTP allo stesso host e allo stesso percorso tramite il protocollo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72750-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="72750-209">Se il codice di stato non viene specificato, il middleware usa come valore predefinito *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="72750-210">Se non viene specificata la porta:</span><span class="sxs-lookup"><span data-stu-id="72750-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="72750-211">Il middleware usa come valore predefinito `null`.</span><span class="sxs-lookup"><span data-stu-id="72750-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="72750-212">Lo schema viene modificato in `https` (protocollo HTTPS) e il client accede alla risorsa attraverso la porta 443.</span><span class="sxs-lookup"><span data-stu-id="72750-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="72750-213">L'esempio seguente illustra come impostare il codice di stato su *301 - Spostato permanentemente* e come passare alla porta 5001.</span><span class="sxs-lookup"><span data-stu-id="72750-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="72750-214">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> per reindirizzare le richieste non protette allo stesso host e allo stesso percorso con il protocollo protetto HTTPS attraverso la porta 443.</span><span class="sxs-lookup"><span data-stu-id="72750-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="72750-215">Il middleware imposta il codice di stato su *301 - Spostato permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="72750-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="72750-216">Per il reindirizzamento a un endpoint protetto quando non sono richieste regole di reindirizzamento aggiuntive, è consigliabile usare il middleware di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72750-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="72750-217">Per altre informazioni, vedere l'argomento [Imporre HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="72750-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="72750-218">L'app di esempio indica come usare `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="72750-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="72750-219">Aggiungere il metodo di estensione a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="72750-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="72750-220">Eseguire una richiesta non sicura all'app su qualsiasi URL.</span><span class="sxs-lookup"><span data-stu-id="72750-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="72750-221">Ignorare l'avviso di sicurezza del browser indicante che il certificato autofirmato non è attendibile oppure creare un'eccezione per rendere affidabile il certificato.</span><span class="sxs-lookup"><span data-stu-id="72750-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="72750-222">Richiesta originale con `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="72750-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="72750-224">Richiesta originale con `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="72750-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="72750-226">Riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-226">URL rewrite</span></span>

<span data-ttu-id="72750-227">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> per creare una regola di riscrittura URL.</span><span class="sxs-lookup"><span data-stu-id="72750-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="72750-228">Il primo parametro contiene l'espressione regolare per la corrispondenza in base al percorso dell'URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="72750-229">Il secondo parametro è la stringa di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="72750-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="72750-230">Il terzo parametro, `skipRemainingRules: {true|false}`, indica al middleware se ignorare le regole di riscrittura aggiuntive quando viene applicata la regola corrente.</span><span class="sxs-lookup"><span data-stu-id="72750-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="72750-231">Richiesta originale: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="72750-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richiesta e risposta](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="72750-233">L'accento circonflesso (`^`) all'inizio dell'espressione indica che la corrispondenza inizia all'inizio del percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="72750-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="72750-234">Nell'esempio precedente della regola di reindirizzamento `redirect-rule/(.*)`, non è presente l'accento circonflesso (`^`) all'inizio dell'espressione regolare.</span><span class="sxs-lookup"><span data-stu-id="72750-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="72750-235">È quindi possibile ottenere una corrispondenza anteponendo qualsiasi carattere a `redirect-rule/`.</span><span class="sxs-lookup"><span data-stu-id="72750-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="72750-236">Percorso</span><span class="sxs-lookup"><span data-stu-id="72750-236">Path</span></span>                               | <span data-ttu-id="72750-237">Corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-237">Match</span></span> |
| ---
<span data-ttu-id="72750-238">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-239">'Blazor'</span></span>
- <span data-ttu-id="72750-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-240">'Identity'</span></span>
- <span data-ttu-id="72750-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-242">'Razor'</span></span>
- <span data-ttu-id="72750-243">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-244">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-245">'Blazor'</span></span>
- <span data-ttu-id="72750-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-246">'Identity'</span></span>
- <span data-ttu-id="72750-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-248">'Razor'</span></span>
- <span data-ttu-id="72750-249">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-250">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-251">'Blazor'</span></span>
- <span data-ttu-id="72750-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-252">'Identity'</span></span>
- <span data-ttu-id="72750-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-254">'Razor'</span></span>
- <span data-ttu-id="72750-255">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-256">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-257">'Blazor'</span></span>
- <span data-ttu-id="72750-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-258">'Identity'</span></span>
- <span data-ttu-id="72750-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-260">'Razor'</span></span>
- <span data-ttu-id="72750-261">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-262">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-263">'Blazor'</span></span>
- <span data-ttu-id="72750-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-264">'Identity'</span></span>
- <span data-ttu-id="72750-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-266">'Razor'</span></span>
- <span data-ttu-id="72750-267">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-268">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-269">'Blazor'</span></span>
- <span data-ttu-id="72750-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-270">'Identity'</span></span>
- <span data-ttu-id="72750-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-272">'Razor'</span></span>
- <span data-ttu-id="72750-273">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-274">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-275">'Blazor'</span></span>
- <span data-ttu-id="72750-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-276">'Identity'</span></span>
- <span data-ttu-id="72750-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-278">'Razor'</span></span>
- <span data-ttu-id="72750-279">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-280">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-281">'Blazor'</span></span>
- <span data-ttu-id="72750-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-282">'Identity'</span></span>
- <span data-ttu-id="72750-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-284">'Razor'</span></span>
- <span data-ttu-id="72750-285">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-286">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-287">'Blazor'</span></span>
- <span data-ttu-id="72750-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-288">'Identity'</span></span>
- <span data-ttu-id="72750-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-290">'Razor'</span></span>
- <span data-ttu-id="72750-291">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-292">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-293">'Blazor'</span></span>
- <span data-ttu-id="72750-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-294">'Identity'</span></span>
- <span data-ttu-id="72750-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-296">'Razor'</span></span>
- <span data-ttu-id="72750-297">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-298">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-299">'Blazor'</span></span>
- <span data-ttu-id="72750-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-300">'Identity'</span></span>
- <span data-ttu-id="72750-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-302">'Razor'</span></span>
- <span data-ttu-id="72750-303">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-304">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-305">'Blazor'</span></span>
- <span data-ttu-id="72750-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-306">'Identity'</span></span>
- <span data-ttu-id="72750-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-308">'Razor'</span></span>
- <span data-ttu-id="72750-309">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-310">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-311">'Blazor'</span></span>
- <span data-ttu-id="72750-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-312">'Identity'</span></span>
- <span data-ttu-id="72750-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-314">'Razor'</span></span>
- <span data-ttu-id="72750-315">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-316">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-317">'Blazor'</span></span>
- <span data-ttu-id="72750-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-318">'Identity'</span></span>
- <span data-ttu-id="72750-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-320">'Razor'</span></span>
- <span data-ttu-id="72750-321">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-322">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-323">'Blazor'</span></span>
- <span data-ttu-id="72750-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-324">'Identity'</span></span>
- <span data-ttu-id="72750-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-326">'Razor'</span></span>
- <span data-ttu-id="72750-327">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-327">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sì | | `/my-cool-redirect-rule/1234/5678` | Sì | | `/anotherredirect-rule/1234/5678`  | Sì |</span><span class="sxs-lookup"><span data-stu-id="72750-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="72750-329">La regola di riscrittura, `^rewrite-rule/(\d+)/(\d+)`, rileva la corrispondenza dei percorsi solo se iniziano con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="72750-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="72750-330">Nella tabella seguente, si noti la differenza nella corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="72750-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="72750-331">Percorso</span><span class="sxs-lookup"><span data-stu-id="72750-331">Path</span></span>                              | <span data-ttu-id="72750-332">Corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-332">Match</span></span> |
| ---
<span data-ttu-id="72750-333">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-334">'Blazor'</span></span>
- <span data-ttu-id="72750-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-335">'Identity'</span></span>
- <span data-ttu-id="72750-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-337">'Razor'</span></span>
- <span data-ttu-id="72750-338">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-339">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-340">'Blazor'</span></span>
- <span data-ttu-id="72750-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-341">'Identity'</span></span>
- <span data-ttu-id="72750-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-343">'Razor'</span></span>
- <span data-ttu-id="72750-344">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-345">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-346">'Blazor'</span></span>
- <span data-ttu-id="72750-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-347">'Identity'</span></span>
- <span data-ttu-id="72750-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-349">'Razor'</span></span>
- <span data-ttu-id="72750-350">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-351">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-352">'Blazor'</span></span>
- <span data-ttu-id="72750-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-353">'Identity'</span></span>
- <span data-ttu-id="72750-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-355">'Razor'</span></span>
- <span data-ttu-id="72750-356">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-357">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-358">'Blazor'</span></span>
- <span data-ttu-id="72750-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-359">'Identity'</span></span>
- <span data-ttu-id="72750-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-361">'Razor'</span></span>
- <span data-ttu-id="72750-362">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-363">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-364">'Blazor'</span></span>
- <span data-ttu-id="72750-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-365">'Identity'</span></span>
- <span data-ttu-id="72750-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-367">'Razor'</span></span>
- <span data-ttu-id="72750-368">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-369">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-370">'Blazor'</span></span>
- <span data-ttu-id="72750-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-371">'Identity'</span></span>
- <span data-ttu-id="72750-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-373">'Razor'</span></span>
- <span data-ttu-id="72750-374">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-375">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-376">'Blazor'</span></span>
- <span data-ttu-id="72750-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-377">'Identity'</span></span>
- <span data-ttu-id="72750-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-379">'Razor'</span></span>
- <span data-ttu-id="72750-380">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-381">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-382">'Blazor'</span></span>
- <span data-ttu-id="72750-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-383">'Identity'</span></span>
- <span data-ttu-id="72750-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-385">'Razor'</span></span>
- <span data-ttu-id="72750-386">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-387">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-388">'Blazor'</span></span>
- <span data-ttu-id="72750-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-389">'Identity'</span></span>
- <span data-ttu-id="72750-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-391">'Razor'</span></span>
- <span data-ttu-id="72750-392">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-393">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-394">'Blazor'</span></span>
- <span data-ttu-id="72750-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-395">'Identity'</span></span>
- <span data-ttu-id="72750-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-397">'Razor'</span></span>
- <span data-ttu-id="72750-398">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-399">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-400">'Blazor'</span></span>
- <span data-ttu-id="72750-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-401">'Identity'</span></span>
- <span data-ttu-id="72750-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-403">'Razor'</span></span>
- <span data-ttu-id="72750-404">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-405">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-406">'Blazor'</span></span>
- <span data-ttu-id="72750-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-407">'Identity'</span></span>
- <span data-ttu-id="72750-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-409">'Razor'</span></span>
- <span data-ttu-id="72750-410">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-411">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-412">'Blazor'</span></span>
- <span data-ttu-id="72750-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-413">'Identity'</span></span>
- <span data-ttu-id="72750-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-415">'Razor'</span></span>
- <span data-ttu-id="72750-416">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-416">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sì | | `/my-cool-rewrite-rule/1234/5678` | No | | `/anotherrewrite-rule/1234/5678`  | No |</span><span class="sxs-lookup"><span data-stu-id="72750-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="72750-418">Dopo la parte `^rewrite-rule/` dell'espressione sono presenti due gruppi Capture, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="72750-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="72750-419">`\d` indica *corrispondenza con una cifra (numero)*.</span><span class="sxs-lookup"><span data-stu-id="72750-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="72750-420">Il segno più (`+`) indica *corrispondenza con uno o più dei caratteri precedenti*.</span><span class="sxs-lookup"><span data-stu-id="72750-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="72750-421">Di conseguenza l'URL deve contenere un numero seguito da una barra seguita a sua volta da un altro numero.</span><span class="sxs-lookup"><span data-stu-id="72750-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="72750-422">Questi gruppi Capture vengono inseriti nell'URL riscritto come `$1` e `$2`.</span><span class="sxs-lookup"><span data-stu-id="72750-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="72750-423">La stringa di sostituzione della regola di riscrittura inserisce i gruppi acquisiti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="72750-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="72750-424">Il percorso richiesto `/rewrite-rule/1234/5678` viene riscritto per ottenere la risorsa in `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="72750-425">Se nella richiesta originale è presente una stringa di query, la stringa viene mantenuta quando l'URL viene riscritto.</span><span class="sxs-lookup"><span data-stu-id="72750-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="72750-426">Non viene eseguito alcun round trip al server per ottenere la risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="72750-427">Se la risorsa esiste, viene recuperata e restituita al client con il codice di stato *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="72750-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="72750-428">Poiché il client non è reindirizzato, l'URL nella barra degli indirizzi del browser non cambia.</span><span class="sxs-lookup"><span data-stu-id="72750-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="72750-429">I client non sono in grado di rilevare che si è verificata un'operazione di riscrittura URL nel server.</span><span class="sxs-lookup"><span data-stu-id="72750-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="72750-430">Se possibile, usare sempre `skipRemainingRules: true`, perché la corrispondenza tra le regole è onerosa dal punto di vista del calcolo e aumenta il tempo di risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="72750-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="72750-431">Per velocizzare il tempo di risposta dell'app:</span><span class="sxs-lookup"><span data-stu-id="72750-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="72750-432">Ordinare le regole di riscrittura dalla regola che origina più corrispondenze a quella che origina meno corrispondenze.</span><span class="sxs-lookup"><span data-stu-id="72750-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="72750-433">Quando viene rilevata una corrispondenza e non sono necessarie altre operazioni di elaborazione delle regole, ignorare l'elaborazione delle regole rimanenti.</span><span class="sxs-lookup"><span data-stu-id="72750-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="72750-434">Modulo Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="72750-434">Apache mod_rewrite</span></span>

<span data-ttu-id="72750-435">Applicare le regole del modulo Apache mod_rewrite con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="72750-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="72750-436">Assicurarsi che il file delle regole venga distribuito con l'app.</span><span class="sxs-lookup"><span data-stu-id="72750-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="72750-437">Per altre informazioni ed esempi di regole mod_rewrite, vedere [Modulo Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="72750-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="72750-438"><xref:System.IO.StreamReader>Viene usato per leggere le regole del file di regole *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="72750-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="72750-439">L'app di esempio reindirizza le richieste da `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="72750-440">Il codice di stato della risposta è *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="72750-441">Richiesta originale: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="72750-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="72750-443">Il middleware supporta le seguenti variabili server del modulo Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="72750-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="72750-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="72750-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="72750-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="72750-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="72750-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="72750-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="72750-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="72750-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="72750-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="72750-449">HTTP_HOST</span></span>
* <span data-ttu-id="72750-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="72750-450">HTTP_REFERER</span></span>
* <span data-ttu-id="72750-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="72750-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="72750-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="72750-452">HTTPS</span></span>
* <span data-ttu-id="72750-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="72750-453">IPV6</span></span>
* <span data-ttu-id="72750-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="72750-454">QUERY_STRING</span></span>
* <span data-ttu-id="72750-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-456">REMOTE_PORT</span></span>
* <span data-ttu-id="72750-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="72750-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="72750-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="72750-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="72750-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="72750-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="72750-460">REQUEST_URI</span></span>
* <span data-ttu-id="72750-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="72750-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-462">SERVER_ADDR</span></span>
* <span data-ttu-id="72750-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-463">SERVER_PORT</span></span>
* <span data-ttu-id="72750-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="72750-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="72750-465">TIME</span><span class="sxs-lookup"><span data-stu-id="72750-465">TIME</span></span>
* <span data-ttu-id="72750-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="72750-466">TIME_DAY</span></span>
* <span data-ttu-id="72750-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="72750-467">TIME_HOUR</span></span>
* <span data-ttu-id="72750-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="72750-468">TIME_MIN</span></span>
* <span data-ttu-id="72750-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="72750-469">TIME_MON</span></span>
* <span data-ttu-id="72750-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="72750-470">TIME_SEC</span></span>
* <span data-ttu-id="72750-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="72750-471">TIME_WDAY</span></span>
* <span data-ttu-id="72750-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="72750-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="72750-473">Regole di IIS URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="72750-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="72750-474">Per usare lo stesso set di regole applicabile a IIS URL Rewrite Module, usare <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="72750-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="72750-475">Assicurarsi che il file delle regole venga distribuito con l'app.</span><span class="sxs-lookup"><span data-stu-id="72750-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="72750-476">Non indirizzare il middleware all'uso del file *web.config* dell'app quando è in esecuzione in Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="72750-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="72750-477">Con IIS queste regole devono essere archiviate al di fuori del file *web.config* dell'app, per evitare conflitti con il modulo IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="72750-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="72750-478">Per altre informazioni ed esempi di regole di IIS URL Rewrite Module, vedere [Using URL Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso di URL Rewrite Module 2.0) e [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Guida di riferimento per la configurazione di URL Rewrite Module).</span><span class="sxs-lookup"><span data-stu-id="72750-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="72750-479"><xref:System.IO.StreamReader>Viene usato per leggere le regole del file di regole *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="72750-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="72750-480">L'app di esempio riscrive le richieste da `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="72750-481">La risposta viene inviata al client con il codice di stato *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="72750-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="72750-482">Richiesta originale: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="72750-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richiesta e risposta](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="72750-484">Se è presente un modulo IIS Rewrite attivo con regole di livello server configurate che possono avere effetti indesiderati nell'app, è possibile disabilitare il modulo IIS Rewrite per un'app.</span><span class="sxs-lookup"><span data-stu-id="72750-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="72750-485">Per altre informazioni, vedere [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Disabilitazione di moduli IIS).</span><span class="sxs-lookup"><span data-stu-id="72750-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="72750-486">Funzionalità non supportate</span><span class="sxs-lookup"><span data-stu-id="72750-486">Unsupported features</span></span>

<span data-ttu-id="72750-487">Il middleware rilasciato con ASP.NET Core 2.x non supporta le seguenti funzionalità di IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="72750-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="72750-488">Regole in uscita</span><span class="sxs-lookup"><span data-stu-id="72750-488">Outbound Rules</span></span>
* <span data-ttu-id="72750-489">Variabili server personalizzate</span><span class="sxs-lookup"><span data-stu-id="72750-489">Custom Server Variables</span></span>
* <span data-ttu-id="72750-490">Caratteri jolly</span><span class="sxs-lookup"><span data-stu-id="72750-490">Wildcards</span></span>
* <span data-ttu-id="72750-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="72750-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="72750-492">Variabili server supportate</span><span class="sxs-lookup"><span data-stu-id="72750-492">Supported server variables</span></span>

<span data-ttu-id="72750-493">Il middleware supporta le seguenti variabili server di IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="72750-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="72750-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="72750-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="72750-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="72750-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="72750-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="72750-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="72750-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="72750-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="72750-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="72750-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="72750-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="72750-499">HTTP_HOST</span></span>
* <span data-ttu-id="72750-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="72750-500">HTTP_REFERER</span></span>
* <span data-ttu-id="72750-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="72750-501">HTTP_URL</span></span>
* <span data-ttu-id="72750-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="72750-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="72750-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="72750-503">HTTPS</span></span>
* <span data-ttu-id="72750-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="72750-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="72750-505">QUERY_STRING</span></span>
* <span data-ttu-id="72750-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-507">REMOTE_PORT</span></span>
* <span data-ttu-id="72750-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="72750-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="72750-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="72750-510">È anche possibile ottenere un <xref:Microsoft.Extensions.FileProviders.IFileProvider> tramite un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="72750-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="72750-511">Questo approccio può garantire maggior flessibilità per la posizione dei file delle regole di riscrittura.</span><span class="sxs-lookup"><span data-stu-id="72750-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="72750-512">Assicurarsi che i file di regole di riscrittura vengano distribuiti nel server in corrispondenza del percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="72750-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="72750-513">Regola basata su metodo</span><span class="sxs-lookup"><span data-stu-id="72750-513">Method-based rule</span></span>

<span data-ttu-id="72750-514">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> per implementare logica della regola personalizzata in un metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="72750-515">`Add` espone <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, che rende disponibile <xref:Microsoft.AspNetCore.Http.HttpContext> per l'uso nel metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="72750-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina la modalità di gestione dell'elaborazione pipeline aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="72750-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="72750-517">Impostare il valore su uno dei campi <xref:Microsoft.AspNetCore.Rewrite.RuleResult> descritti nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="72750-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="72750-518">Azione</span><span class="sxs-lookup"><span data-stu-id="72750-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="72750-519">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-520">'Blazor'</span></span>
- <span data-ttu-id="72750-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-521">'Identity'</span></span>
- <span data-ttu-id="72750-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-523">'Razor'</span></span>
- <span data-ttu-id="72750-524">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-525">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-526">'Blazor'</span></span>
- <span data-ttu-id="72750-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-527">'Identity'</span></span>
- <span data-ttu-id="72750-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-529">'Razor'</span></span>
- <span data-ttu-id="72750-530">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-531">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-532">'Blazor'</span></span>
- <span data-ttu-id="72750-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-533">'Identity'</span></span>
- <span data-ttu-id="72750-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-535">'Razor'</span></span>
- <span data-ttu-id="72750-536">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-537">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-538">'Blazor'</span></span>
- <span data-ttu-id="72750-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-539">'Identity'</span></span>
- <span data-ttu-id="72750-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-541">'Razor'</span></span>
- <span data-ttu-id="72750-542">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-543">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-544">'Blazor'</span></span>
- <span data-ttu-id="72750-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-545">'Identity'</span></span>
- <span data-ttu-id="72750-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-547">'Razor'</span></span>
- <span data-ttu-id="72750-548">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-549">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-550">'Blazor'</span></span>
- <span data-ttu-id="72750-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-551">'Identity'</span></span>
- <span data-ttu-id="72750-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-553">'Razor'</span></span>
- <span data-ttu-id="72750-554">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-555">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-556">'Blazor'</span></span>
- <span data-ttu-id="72750-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-557">'Identity'</span></span>
- <span data-ttu-id="72750-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-559">'Razor'</span></span>
- <span data-ttu-id="72750-560">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-561">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-562">'Blazor'</span></span>
- <span data-ttu-id="72750-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-563">'Identity'</span></span>
- <span data-ttu-id="72750-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-565">'Razor'</span></span>
- <span data-ttu-id="72750-566">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-567">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-568">'Blazor'</span></span>
- <span data-ttu-id="72750-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-569">'Identity'</span></span>
- <span data-ttu-id="72750-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-571">'Razor'</span></span>
- <span data-ttu-id="72750-572">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-573">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-574">'Blazor'</span></span>
- <span data-ttu-id="72750-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-575">'Identity'</span></span>
- <span data-ttu-id="72750-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-577">'Razor'</span></span>
- <span data-ttu-id="72750-578">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-579">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-580">'Blazor'</span></span>
- <span data-ttu-id="72750-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-581">'Identity'</span></span>
- <span data-ttu-id="72750-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-583">'Razor'</span></span>
- <span data-ttu-id="72750-584">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-585">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-586">'Blazor'</span></span>
- <span data-ttu-id="72750-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-587">'Identity'</span></span>
- <span data-ttu-id="72750-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-589">'Razor'</span></span>
- <span data-ttu-id="72750-590">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-591">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-592">'Blazor'</span></span>
- <span data-ttu-id="72750-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-593">'Identity'</span></span>
- <span data-ttu-id="72750-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-595">'Razor'</span></span>
- <span data-ttu-id="72750-596">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-597">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-598">'Blazor'</span></span>
- <span data-ttu-id="72750-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-599">'Identity'</span></span>
- <span data-ttu-id="72750-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-601">'Razor'</span></span>
- <span data-ttu-id="72750-602">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-603">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-604">'Blazor'</span></span>
- <span data-ttu-id="72750-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-605">'Identity'</span></span>
- <span data-ttu-id="72750-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-607">'Razor'</span></span>
- <span data-ttu-id="72750-608">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-609">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-610">'Blazor'</span></span>
- <span data-ttu-id="72750-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-611">'Identity'</span></span>
- <span data-ttu-id="72750-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-613">'Razor'</span></span>
- <span data-ttu-id="72750-614">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-614">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-615">------------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-616">'Blazor'</span></span>
- <span data-ttu-id="72750-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-617">'Identity'</span></span>
- <span data-ttu-id="72750-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-619">'Razor'</span></span>
- <span data-ttu-id="72750-620">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-621">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-622">'Blazor'</span></span>
- <span data-ttu-id="72750-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-623">'Identity'</span></span>
- <span data-ttu-id="72750-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-625">'Razor'</span></span>
- <span data-ttu-id="72750-626">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-627">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-628">'Blazor'</span></span>
- <span data-ttu-id="72750-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-629">'Identity'</span></span>
- <span data-ttu-id="72750-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-631">'Razor'</span></span>
- <span data-ttu-id="72750-632">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-633">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-634">'Blazor'</span></span>
- <span data-ttu-id="72750-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-635">'Identity'</span></span>
- <span data-ttu-id="72750-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-637">'Razor'</span></span>
- <span data-ttu-id="72750-638">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-639">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-640">'Blazor'</span></span>
- <span data-ttu-id="72750-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-641">'Identity'</span></span>
- <span data-ttu-id="72750-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-643">'Razor'</span></span>
- <span data-ttu-id="72750-644">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-645">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-646">'Blazor'</span></span>
- <span data-ttu-id="72750-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-647">'Identity'</span></span>
- <span data-ttu-id="72750-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-649">'Razor'</span></span>
- <span data-ttu-id="72750-650">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-651">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-652">'Blazor'</span></span>
- <span data-ttu-id="72750-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-653">'Identity'</span></span>
- <span data-ttu-id="72750-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-655">'Razor'</span></span>
- <span data-ttu-id="72750-656">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-657">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-658">'Blazor'</span></span>
- <span data-ttu-id="72750-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-659">'Identity'</span></span>
- <span data-ttu-id="72750-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-661">'Razor'</span></span>
- <span data-ttu-id="72750-662">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-663">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-664">'Blazor'</span></span>
- <span data-ttu-id="72750-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-665">'Identity'</span></span>
- <span data-ttu-id="72750-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-667">'Razor'</span></span>
- <span data-ttu-id="72750-668">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-669">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-670">'Blazor'</span></span>
- <span data-ttu-id="72750-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-671">'Identity'</span></span>
- <span data-ttu-id="72750-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-673">'Razor'</span></span>
- <span data-ttu-id="72750-674">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-675">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-676">'Blazor'</span></span>
- <span data-ttu-id="72750-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-677">'Identity'</span></span>
- <span data-ttu-id="72750-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-679">'Razor'</span></span>
- <span data-ttu-id="72750-680">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-681">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-682">'Blazor'</span></span>
- <span data-ttu-id="72750-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-683">'Identity'</span></span>
- <span data-ttu-id="72750-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-685">'Razor'</span></span>
- <span data-ttu-id="72750-686">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-687">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-688">'Blazor'</span></span>
- <span data-ttu-id="72750-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-689">'Identity'</span></span>
- <span data-ttu-id="72750-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-691">'Razor'</span></span>
- <span data-ttu-id="72750-692">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-693">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-694">'Blazor'</span></span>
- <span data-ttu-id="72750-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-695">'Identity'</span></span>
- <span data-ttu-id="72750-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-697">'Razor'</span></span>
- <span data-ttu-id="72750-698">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-699">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-700">'Blazor'</span></span>
- <span data-ttu-id="72750-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-701">'Identity'</span></span>
- <span data-ttu-id="72750-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-703">'Razor'</span></span>
- <span data-ttu-id="72750-704">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-705">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-706">'Blazor'</span></span>
- <span data-ttu-id="72750-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-707">'Identity'</span></span>
- <span data-ttu-id="72750-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-709">'Razor'</span></span>
- <span data-ttu-id="72750-710">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-711">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-712">'Blazor'</span></span>
- <span data-ttu-id="72750-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-713">'Identity'</span></span>
- <span data-ttu-id="72750-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-715">'Razor'</span></span>
- <span data-ttu-id="72750-716">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-717">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-718">'Blazor'</span></span>
- <span data-ttu-id="72750-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-719">'Identity'</span></span>
- <span data-ttu-id="72750-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-721">'Razor'</span></span>
- <span data-ttu-id="72750-722">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-723">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-724">'Blazor'</span></span>
- <span data-ttu-id="72750-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-725">'Identity'</span></span>
- <span data-ttu-id="72750-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-727">'Razor'</span></span>
- <span data-ttu-id="72750-728">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-729">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-730">'Blazor'</span></span>
- <span data-ttu-id="72750-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-731">'Identity'</span></span>
- <span data-ttu-id="72750-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-733">'Razor'</span></span>
- <span data-ttu-id="72750-734">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-735">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-736">'Blazor'</span></span>
- <span data-ttu-id="72750-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-737">'Identity'</span></span>
- <span data-ttu-id="72750-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-739">'Razor'</span></span>
- <span data-ttu-id="72750-740">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-741">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-742">'Blazor'</span></span>
- <span data-ttu-id="72750-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-743">'Identity'</span></span>
- <span data-ttu-id="72750-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-745">'Razor'</span></span>
- <span data-ttu-id="72750-746">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-747">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-748">'Blazor'</span></span>
- <span data-ttu-id="72750-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-749">'Identity'</span></span>
- <span data-ttu-id="72750-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-751">'Razor'</span></span>
- <span data-ttu-id="72750-752">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-753">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-754">'Blazor'</span></span>
- <span data-ttu-id="72750-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-755">'Identity'</span></span>
- <span data-ttu-id="72750-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-757">'Razor'</span></span>
- <span data-ttu-id="72750-758">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-759">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-760">'Blazor'</span></span>
- <span data-ttu-id="72750-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-761">'Identity'</span></span>
- <span data-ttu-id="72750-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-763">'Razor'</span></span>
- <span data-ttu-id="72750-764">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-765">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-766">'Blazor'</span></span>
- <span data-ttu-id="72750-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-767">'Identity'</span></span>
- <span data-ttu-id="72750-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-769">'Razor'</span></span>
- <span data-ttu-id="72750-770">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-771">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-772">'Blazor'</span></span>
- <span data-ttu-id="72750-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-773">'Identity'</span></span>
- <span data-ttu-id="72750-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-775">'Razor'</span></span>
- <span data-ttu-id="72750-776">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-777">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-778">'Blazor'</span></span>
- <span data-ttu-id="72750-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-779">'Identity'</span></span>
- <span data-ttu-id="72750-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-781">'Razor'</span></span>
- <span data-ttu-id="72750-782">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-783">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-784">'Blazor'</span></span>
- <span data-ttu-id="72750-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-785">'Identity'</span></span>
- <span data-ttu-id="72750-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-787">'Razor'</span></span>
- <span data-ttu-id="72750-788">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-789">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-790">'Blazor'</span></span>
- <span data-ttu-id="72750-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-791">'Identity'</span></span>
- <span data-ttu-id="72750-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-793">'Razor'</span></span>
- <span data-ttu-id="72750-794">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-794">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-795">-------------------------------- | | `RuleResult.ContinueRules`(impostazione predefinita) | Continuare a applicare le regole.</span><span class="sxs-lookup"><span data-stu-id="72750-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="72750-796">| | `RuleResult.EndResponse`             | Interrompere l'applicazione delle regole e inviare la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="72750-797">| | `RuleResult.SkipRemainingRules`      | Interrompere l'applicazione delle regole e inviare il contesto al middleware successivo.</span><span class="sxs-lookup"><span data-stu-id="72750-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="72750-798">L'app di esempio visualizza un metodo che reindirizza le richieste per i percorsi che terminano con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="72750-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="72750-799">Se viene inviata una richiesta per `/file.xml`, la richiesta viene reindirizzata a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="72750-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="72750-800">Il codice di stato viene impostato su *301 - Spostato permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="72750-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="72750-801">Quando il browser invia una nuova richiesta per */xmlfiles/file.xml*, il middleware dei file statici rende disponibile il file al client dalla cartella *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="72750-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="72750-802">Per un reindirizzamento, impostare in modo esplicito il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="72750-803">In caso contrario, viene restituito il codice di stato *200 - OK* e il reindirizzamento non viene eseguito nel client.</span><span class="sxs-lookup"><span data-stu-id="72750-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="72750-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="72750-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="72750-805">Questo approccio consente anche di riscrivere le richieste.</span><span class="sxs-lookup"><span data-stu-id="72750-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="72750-806">L'app di esempio illustra la riscrittura del percorso di una richiesta di file di testo per rendere disponibile il file di testo *file. txt* dalla cartella *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="72750-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="72750-807">Il middleware dei file statici rende disponibile il file in base al percorso di richiesta aggiornato:</span><span class="sxs-lookup"><span data-stu-id="72750-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="72750-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="72750-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="72750-809">Regola basata su IRule</span><span class="sxs-lookup"><span data-stu-id="72750-809">IRule-based rule</span></span>

<span data-ttu-id="72750-810">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> per usare la logica della regola in una classe che implementa l'interfaccia <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="72750-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="72750-811">`IRule` garantisce maggiore flessibilità rispetto all'approccio con una regola basata su un metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="72750-812">La classe di implementazione può includere un costruttore, che consente di passare parametri per il metodo <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="72750-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="72750-813">I valori dei parametri nell'app di esempio per `extension` e `newPath` vengono verificati e devono soddisfare diverse condizioni.</span><span class="sxs-lookup"><span data-stu-id="72750-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="72750-814">`extension` deve contenere un valore e tale valore deve essere *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="72750-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="72750-815">Se `newPath` non è valido, viene generato un evento <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="72750-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="72750-816">Se viene inviata una richiesta per *image.png*, la richiesta viene reindirizzata a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="72750-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="72750-817">Se viene inviata una richiesta per *image.jpg*, la richiesta viene reindirizzata a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="72750-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="72750-818">Il codice di stato viene impostato su *301 - Spostato permanentemente* e l'elemento `context.Result` viene impostato per l'interruzione dell'elaborazione delle regole e l'invio della risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="72750-819">Richiesta originale: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="72750-819">Original Request: `/image.png`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte per image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="72750-821">Richiesta originale: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="72750-821">Original Request: `/image.jpg`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte per image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="72750-823">Esempi di espressioni regolari</span><span class="sxs-lookup"><span data-stu-id="72750-823">Regex examples</span></span>

| <span data-ttu-id="72750-824">Obiettivo</span><span class="sxs-lookup"><span data-stu-id="72750-824">Goal</span></span> | <span data-ttu-id="72750-825">Esempio di stringa espressione regolare</span><span class="sxs-lookup"><span data-stu-id="72750-825">Regex String &</span></span><br><span data-ttu-id="72750-826">e corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-826">Match Example</span></span> | <span data-ttu-id="72750-827">Esempio di stringa di sostituzione</span><span class="sxs-lookup"><span data-stu-id="72750-827">Replacement String &</span></span><br><span data-ttu-id="72750-828">e output</span><span class="sxs-lookup"><span data-stu-id="72750-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="72750-829">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-830">'Blazor'</span></span>
- <span data-ttu-id="72750-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-831">'Identity'</span></span>
- <span data-ttu-id="72750-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-833">'Razor'</span></span>
- <span data-ttu-id="72750-834">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-835">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-836">'Blazor'</span></span>
- <span data-ttu-id="72750-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-837">'Identity'</span></span>
- <span data-ttu-id="72750-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-839">'Razor'</span></span>
- <span data-ttu-id="72750-840">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-841">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-842">'Blazor'</span></span>
- <span data-ttu-id="72750-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-843">'Identity'</span></span>
- <span data-ttu-id="72750-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-845">'Razor'</span></span>
- <span data-ttu-id="72750-846">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-847">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-848">'Blazor'</span></span>
- <span data-ttu-id="72750-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-849">'Identity'</span></span>
- <span data-ttu-id="72750-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-851">'Razor'</span></span>
- <span data-ttu-id="72750-852">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-853">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-854">'Blazor'</span></span>
- <span data-ttu-id="72750-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-855">'Identity'</span></span>
- <span data-ttu-id="72750-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-857">'Razor'</span></span>
- <span data-ttu-id="72750-858">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-859">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-860">'Blazor'</span></span>
- <span data-ttu-id="72750-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-861">'Identity'</span></span>
- <span data-ttu-id="72750-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-863">'Razor'</span></span>
- <span data-ttu-id="72750-864">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-865">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-866">'Blazor'</span></span>
- <span data-ttu-id="72750-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-867">'Identity'</span></span>
- <span data-ttu-id="72750-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-869">'Razor'</span></span>
- <span data-ttu-id="72750-870">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-871">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-872">'Blazor'</span></span>
- <span data-ttu-id="72750-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-873">'Identity'</span></span>
- <span data-ttu-id="72750-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-875">'Razor'</span></span>
- <span data-ttu-id="72750-876">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-877">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-878">'Blazor'</span></span>
- <span data-ttu-id="72750-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-879">'Identity'</span></span>
- <span data-ttu-id="72750-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-881">'Razor'</span></span>
- <span data-ttu-id="72750-882">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-883">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-884">'Blazor'</span></span>
- <span data-ttu-id="72750-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-885">'Identity'</span></span>
- <span data-ttu-id="72750-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-887">'Razor'</span></span>
- <span data-ttu-id="72750-888">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-889">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-890">'Blazor'</span></span>
- <span data-ttu-id="72750-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-891">'Identity'</span></span>
- <span data-ttu-id="72750-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-893">'Razor'</span></span>
- <span data-ttu-id="72750-894">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-895">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-896">'Blazor'</span></span>
- <span data-ttu-id="72750-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-897">'Identity'</span></span>
- <span data-ttu-id="72750-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-899">'Razor'</span></span>
- <span data-ttu-id="72750-900">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-901">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-902">'Blazor'</span></span>
- <span data-ttu-id="72750-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-903">'Identity'</span></span>
- <span data-ttu-id="72750-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-905">'Razor'</span></span>
- <span data-ttu-id="72750-906">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-906">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-907">---------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-908">'Blazor'</span></span>
- <span data-ttu-id="72750-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-909">'Identity'</span></span>
- <span data-ttu-id="72750-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-911">'Razor'</span></span>
- <span data-ttu-id="72750-912">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-913">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-914">'Blazor'</span></span>
- <span data-ttu-id="72750-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-915">'Identity'</span></span>
- <span data-ttu-id="72750-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-917">'Razor'</span></span>
- <span data-ttu-id="72750-918">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-919">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-920">'Blazor'</span></span>
- <span data-ttu-id="72750-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-921">'Identity'</span></span>
- <span data-ttu-id="72750-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-923">'Razor'</span></span>
- <span data-ttu-id="72750-924">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-925">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-926">'Blazor'</span></span>
- <span data-ttu-id="72750-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-927">'Identity'</span></span>
- <span data-ttu-id="72750-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-929">'Razor'</span></span>
- <span data-ttu-id="72750-930">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-931">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-932">'Blazor'</span></span>
- <span data-ttu-id="72750-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-933">'Identity'</span></span>
- <span data-ttu-id="72750-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-935">'Razor'</span></span>
- <span data-ttu-id="72750-936">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-937">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-938">'Blazor'</span></span>
- <span data-ttu-id="72750-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-939">'Identity'</span></span>
- <span data-ttu-id="72750-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-941">'Razor'</span></span>
- <span data-ttu-id="72750-942">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-943">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-944">'Blazor'</span></span>
- <span data-ttu-id="72750-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-945">'Identity'</span></span>
- <span data-ttu-id="72750-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-947">'Razor'</span></span>
- <span data-ttu-id="72750-948">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-949">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-950">'Blazor'</span></span>
- <span data-ttu-id="72750-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-951">'Identity'</span></span>
- <span data-ttu-id="72750-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-953">'Razor'</span></span>
- <span data-ttu-id="72750-954">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-955">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-956">'Blazor'</span></span>
- <span data-ttu-id="72750-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-957">'Identity'</span></span>
- <span data-ttu-id="72750-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-959">'Razor'</span></span>
- <span data-ttu-id="72750-960">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-961">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-962">'Blazor'</span></span>
- <span data-ttu-id="72750-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-963">'Identity'</span></span>
- <span data-ttu-id="72750-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-965">'Razor'</span></span>
- <span data-ttu-id="72750-966">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-967">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-968">'Blazor'</span></span>
- <span data-ttu-id="72750-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-969">'Identity'</span></span>
- <span data-ttu-id="72750-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-971">'Razor'</span></span>
- <span data-ttu-id="72750-972">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-973">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-974">'Blazor'</span></span>
- <span data-ttu-id="72750-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-975">'Identity'</span></span>
- <span data-ttu-id="72750-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-977">'Razor'</span></span>
- <span data-ttu-id="72750-978">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-979">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-980">'Blazor'</span></span>
- <span data-ttu-id="72750-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-981">'Identity'</span></span>
- <span data-ttu-id="72750-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-983">'Razor'</span></span>
- <span data-ttu-id="72750-984">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-985">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-986">'Blazor'</span></span>
- <span data-ttu-id="72750-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-987">'Identity'</span></span>
- <span data-ttu-id="72750-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-989">'Razor'</span></span>
- <span data-ttu-id="72750-990">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-991">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-992">'Blazor'</span></span>
- <span data-ttu-id="72750-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-993">'Identity'</span></span>
- <span data-ttu-id="72750-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-995">'Razor'</span></span>
- <span data-ttu-id="72750-996">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-997">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-998">'Blazor'</span></span>
- <span data-ttu-id="72750-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-999">'Identity'</span></span>
- <span data-ttu-id="72750-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1001">'Razor'</span></span>
- <span data-ttu-id="72750-1002">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1003">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1004">'Blazor'</span></span>
- <span data-ttu-id="72750-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1005">'Identity'</span></span>
- <span data-ttu-id="72750-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1007">'Razor'</span></span>
- <span data-ttu-id="72750-1008">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1009">------------------- | | Riscrivere il percorso in QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="72750-1010">`/path?var1=abc&var2=123`| | Barra finale della striscia |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="72750-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="72750-1011">`/path`| | Imponi barra finale |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="72750-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="72750-1012">`/path/`| | Evitare la riscrittura di richieste specifiche | `^(.*)(?<!\.axd)$`o`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="72750-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="72750-1013">Sì: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="72750-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="72750-1014">No:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="72750-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="72750-1015">`/resource.axd`| | Ridisporre i segmenti di URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="72750-1016">`path/3/2/1`| | Sostituire un segmento URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="72750-1017">Questo documento presenta la riscrittura URL con istruzioni per l'uso del middleware Riscrittura URL nelle applicazioni ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="72750-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="72750-1018">La riscrittura URL è l'azione di modificare gli URL di richiesta in base a una o più regole predefinite.</span><span class="sxs-lookup"><span data-stu-id="72750-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="72750-1019">Il processo di riscrittura URL crea un'astrazione tra i percorsi delle risorse e i relativi indirizzi, in modo che i percorsi e gli indirizzi non risultino strettamente collegati.</span><span class="sxs-lookup"><span data-stu-id="72750-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="72750-1020">La riscrittura URL risulta utile in diversi scenari per:</span><span class="sxs-lookup"><span data-stu-id="72750-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="72750-1021">Spostare o sostituire in modo temporaneo o permanente risorse server mantenendo localizzatori stabili di queste risorse.</span><span class="sxs-lookup"><span data-stu-id="72750-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="72750-1022">Suddividere l'elaborazione delle richieste tra app diverse o tra aree diverse di un'unica app.</span><span class="sxs-lookup"><span data-stu-id="72750-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="72750-1023">Rimuovere, aggiungere o riorganizzare segmenti URL nelle richieste in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="72750-1024">Ottimizzare gli URL pubblici per l'ottimizzazione motore di ricerca (SEO, Search Engine Optimization).</span><span class="sxs-lookup"><span data-stu-id="72750-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="72750-1025">Consentire l'uso di URL pubblici descrittivi in modo che i visitatori possano prevedere il contenuto restituito dalla richiesta di una risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="72750-1026">Reindirizzare le richieste non protette a endpoint protetti.</span><span class="sxs-lookup"><span data-stu-id="72750-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="72750-1027">Evitare l'hotlinking, ovvero l'uso da parte di un sito esterno di una risorsa statica ospitata presente in un altro sito tramite il collegamento di questa al proprio contenuto.</span><span class="sxs-lookup"><span data-stu-id="72750-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="72750-1028">La riscrittura URL può ridurre le prestazioni di un'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="72750-1029">Ove possibile, limitare il numero e la complessità delle regole.</span><span class="sxs-lookup"><span data-stu-id="72750-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="72750-1030">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="72750-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="72750-1031">Reindirizzamento URL e riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="72750-1032">La differenza tra i termini *reindirizzamento URL* e *riscrittura URL* è minima, ma ha implicazioni importanti nell'offerta di risorse ai clienti.</span><span class="sxs-lookup"><span data-stu-id="72750-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="72750-1033">Il middleware Riscrittura URL di ASP.NET Core è in grado di svolgere entrambe le funzioni.</span><span class="sxs-lookup"><span data-stu-id="72750-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="72750-1034">Un *reindirizzamento URL* implica un'operazione lato client, in cui viene richiesto al client di accedere a una risorsa a un indirizzo diverso da quello richiesto originariamente dal client.</span><span class="sxs-lookup"><span data-stu-id="72750-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="72750-1035">Questa operazione richiede un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="72750-1036">L'URL di reindirizzamento restituito al client viene visualizzato nella barra degli indirizzi del browser quando il client effettua una nuova richiesta per la risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="72750-1037">In caso di *reindirizzamento* di `/resource` a `/different-resource`, il server risponde che il client deve ottenere la risorsa presso `/different-resource` con un codice di stato che indica se il reindirizzamento è temporaneo o permanente.</span><span class="sxs-lookup"><span data-stu-id="72750-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Un endpoint di servizio WebAPI è stato modificato temporaneamente dalla versione 1 (v1) alla versione 2 (v2) nel server.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="72750-1043">Quando si reindirizzano le richieste a un URL diverso, indicare se il reindirizzamento è temporaneo o permanente specificando il codice di stato con la risposta:</span><span class="sxs-lookup"><span data-stu-id="72750-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="72750-1044">Il codice di stato *301 - Spostato permanentemente* viene usato se la risorsa ha un nuovo URL definitivo e si vuole indicare al client che tale URL dovrà essere usato per tutte le richieste future della risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="72750-1045">*Quando riceve un codice di stato 301, il client può memorizzare la risposta nella cache e riusarla.*</span><span class="sxs-lookup"><span data-stu-id="72750-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="72750-1046">Il codice di stato *302 - Trovato* viene usato quando il reindirizzamento è temporaneo o comunque soggetto a modifiche.</span><span class="sxs-lookup"><span data-stu-id="72750-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="72750-1047">Il codice di stato 302 indica che il client non dovrà archiviare e riusare l'URL di reindirizzamento in futuro.</span><span class="sxs-lookup"><span data-stu-id="72750-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="72750-1048">Per altre informazioni sui codici di stato, vedere [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (RFC 2616: Definizioni dei codici di stato).</span><span class="sxs-lookup"><span data-stu-id="72750-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="72750-1049">La *riscrittura URL* è un'operazione lato server che rende disponibile una risorsa da un indirizzo diverso da quello richiesto dal client.</span><span class="sxs-lookup"><span data-stu-id="72750-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="72750-1050">La riscrittura URL non richiede un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="72750-1051">L'URL riscritto non viene restituito al client e non viene visualizzato nella barra degli indirizzi del browser.</span><span class="sxs-lookup"><span data-stu-id="72750-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="72750-1052">Se `/resource` viene *riscritto* in `/different-resource`, il server recupera *internamente* la risorsa in `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="72750-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="72750-1053">Anche se il client fosse in grado di recuperare la risorsa nell'URL riscritto, non viene informato dell'esistenza della risorsa nell'URL riscritto quando effettua la richiesta e riceve la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Un endpoint servizio WebAPI è stato modificato dalla versione 1 (v1) alla versione 2 (v2) sul server.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="72750-1058">App di esempio per la riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-1058">URL rewriting sample app</span></span>

<span data-ttu-id="72750-1059">È possibile esplorare le funzionalità del middleware Riscrittura URL con l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="72750-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="72750-1060">L'app applica regole di reindirizzamento e riscrittura e visualizza l'URL reindirizzato o riscritto per diversi scenari.</span><span class="sxs-lookup"><span data-stu-id="72750-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="72750-1061">Quando usare il middleware Riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="72750-1062">Usare il middleware Riscrittura URL quando non è possibile usare gli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="72750-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="72750-1063">URL Rewrite Module for IIS in Windows Server</span><span class="sxs-lookup"><span data-stu-id="72750-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="72750-1064">Modulo Apache mod_rewrite in Apache Server</span><span class="sxs-lookup"><span data-stu-id="72750-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="72750-1065">Riscrittura URL in Nginx</span><span class="sxs-lookup"><span data-stu-id="72750-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="72750-1066">Usare il middleware anche se l'app è ospitata in un [server HTTP.sys](xref:fundamentals/servers/httpsys) (in precedenza denominato WebListener).</span><span class="sxs-lookup"><span data-stu-id="72750-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="72750-1067">I motivi principali per usare tecnologie di riscrittura URL basate su server in IIS, Apache e Nginx sono:</span><span class="sxs-lookup"><span data-stu-id="72750-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="72750-1068">Il middleware non supporta tutte le funzionalità di questi moduli.</span><span class="sxs-lookup"><span data-stu-id="72750-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="72750-1069">Alcune funzionalità dei moduli server non funzionano con i progetti ASP.NET Core, ad esempio i vincoli `IsFile` e `IsDirectory` del modulo IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="72750-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="72750-1070">In questi scenari è necessario usare il middleware.</span><span class="sxs-lookup"><span data-stu-id="72750-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="72750-1071">Le prestazioni del middleware probabilmente non corrispondono a quelle dei moduli.</span><span class="sxs-lookup"><span data-stu-id="72750-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="72750-1072">Il benchmarking rappresenta l'unico modo per sapere con certezza quale approccio comporta la maggior riduzione delle prestazioni o se tale riduzione è trascurabile.</span><span class="sxs-lookup"><span data-stu-id="72750-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="72750-1073">Pacchetto</span><span class="sxs-lookup"><span data-stu-id="72750-1073">Package</span></span>

<span data-ttu-id="72750-1074">Per includere il middleware nel progetto, aggiungere un riferimento al pacchetto al [metapacchetto Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nel file di progetto, che contiene il pacchetto [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="72750-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="72750-1075">Se non si usa il metapacchetto `Microsoft.AspNetCore.App`, aggiungere un riferimento al progetto al pacchetto `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="72750-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="72750-1076">Estensioni e opzioni</span><span class="sxs-lookup"><span data-stu-id="72750-1076">Extension and options</span></span>

<span data-ttu-id="72750-1077">È possibile definire regole di riscrittura e reindirizzamento URL creando un'istanza della classe [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) con metodi di estensione per le singole regole di riscrittura.</span><span class="sxs-lookup"><span data-stu-id="72750-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="72750-1078">Concatenare più regole nell'ordine in cui si vuole che vengano elaborate.</span><span class="sxs-lookup"><span data-stu-id="72750-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="72750-1079">Le opzioni `RewriteOptions` vengono passate al middleware Riscrittura URL quando questo viene aggiunto alla pipeline della richiesta con <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="72750-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="72750-1080">Reindirizzare non-www su www</span><span class="sxs-lookup"><span data-stu-id="72750-1080">Redirect non-www to www</span></span>

<span data-ttu-id="72750-1081">Sono disponibili tre opzioni che consentono all'app di reindirizzare richieste non-`www` su `www`:</span><span class="sxs-lookup"><span data-stu-id="72750-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="72750-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Reindirizza in modo permanente la richiesta al `www` sottodominio se la richiesta è non `www` .</span><span class="sxs-lookup"><span data-stu-id="72750-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="72750-1083">Reindirizza con un codice di stato [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="72750-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="72750-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Reindirizza la richiesta al `www` sottodominio se la richiesta in ingresso è non `www` .</span><span class="sxs-lookup"><span data-stu-id="72750-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="72750-1085">Reindirizza con un codice di stato [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="72750-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="72750-1086">Un overload consente di specificare il codice di stato per la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="72750-1087">Usare un campo della classe <xref:Microsoft.AspNetCore.Http.StatusCodes> per un'assegnazione di codice di stato.</span><span class="sxs-lookup"><span data-stu-id="72750-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="72750-1088">Reindirizzamento URL</span><span class="sxs-lookup"><span data-stu-id="72750-1088">URL redirect</span></span>

<span data-ttu-id="72750-1089">Per reindirizzare le richieste, usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>.</span><span class="sxs-lookup"><span data-stu-id="72750-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="72750-1090">Il primo parametro contiene l'espressione regolare per la corrispondenza in base al percorso dell'URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="72750-1091">Il secondo parametro è la stringa di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="72750-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="72750-1092">Il terzo parametro, se presente, specifica il codice di stato.</span><span class="sxs-lookup"><span data-stu-id="72750-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="72750-1093">Se il codice di stato non è specificato, assume come valore predefinito *302 - Trovato*, che indica che la risorsa è stata temporaneamente spostata o sostituita.</span><span class="sxs-lookup"><span data-stu-id="72750-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="72750-1094">In un browser con gli strumenti di sviluppo abilitati, creare una richiesta all'app di esempio con il percorso `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="72750-1095">L'espressione regolare definisce la corrispondenza con il percorso di richiesta in `redirect-rule/(.*)` e il percorso viene sostituito con `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="72750-1096">L'URL di reindirizzamento viene restituito al client con il codice di stato *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="72750-1097">Il browser invia una nuova richiesta all'URL di reindirizzamento, che viene visualizzata nella barra degli indirizzi del browser.</span><span class="sxs-lookup"><span data-stu-id="72750-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="72750-1098">Poiché nessuna regola nell'app di esempio corrisponde all'URL di reindirizzamento:</span><span class="sxs-lookup"><span data-stu-id="72750-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="72750-1099">La seconda richiesta riceve una risposta *200 - OK* dall'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="72750-1100">Il corpo della risposta visualizza l'URL di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="72750-1101">Quando un URL viene *reindirizzato*, viene eseguito un round trip al server.</span><span class="sxs-lookup"><span data-stu-id="72750-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="72750-1102">Prestare attenzione quando si definiscono regole di reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="72750-1103">Le regole di reindirizzamento vengono valutate in ogni richiesta all'app, anche dopo un reindirizzamento.</span><span class="sxs-lookup"><span data-stu-id="72750-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="72750-1104">È facile creare inavvertitamente un *ciclo di reindirizzamento infinito*.</span><span class="sxs-lookup"><span data-stu-id="72750-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="72750-1105">Richiesta originale: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="72750-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="72750-1107">La parte dell'espressione racchiusa tra parentesi è denominata *gruppo Capture*.</span><span class="sxs-lookup"><span data-stu-id="72750-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="72750-1108">Il punto (`.`) dell'espressione significa *corrispondenza con qualsiasi carattere*.</span><span class="sxs-lookup"><span data-stu-id="72750-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="72750-1109">L'asterisco (`*`) indica *corrispondenza con il carattere precedente per zero o più volte*.</span><span class="sxs-lookup"><span data-stu-id="72750-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="72750-1110">Di conseguenza gli ultimi due i segmenti di percorso dell'URL `1234/5678` vengono acquisiti tramite il gruppo Capture `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="72750-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="72750-1111">Qualsiasi valore visualizzato nell'URL della richiesta dopo `redirect-rule/` viene acquisito da questo gruppo Capture.</span><span class="sxs-lookup"><span data-stu-id="72750-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="72750-1112">Nella stringa di sostituzione i gruppi acquisiti vengono inseriti nella stringa con il simbolo di dollaro (`$`) seguito dal numero di sequenza dell'acquisizione.</span><span class="sxs-lookup"><span data-stu-id="72750-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="72750-1113">Il primo valore del gruppo Capture viene ottenuto con `$1`, il secondo con `$2` e la procedura continua in sequenza per i gruppi Capture presenti nell'espressione regolare.</span><span class="sxs-lookup"><span data-stu-id="72750-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="72750-1114">Nell'espressione regolare della regola di reindirizzamento dell'app di esempio è presente un solo gruppo acquisito, pertanto nella stringa di sostituzione è presente un solo gruppo inserito, ovvero `$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="72750-1115">Quando viene applicata la regola, l'URL diventa `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="72750-1116">Reindirizzamento URL a un endpoint protetto</span><span class="sxs-lookup"><span data-stu-id="72750-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="72750-1117">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> per reindirizzare le richieste HTTP allo stesso host e allo stesso percorso tramite il protocollo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72750-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="72750-1118">Se il codice di stato non viene specificato, il middleware usa come valore predefinito *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="72750-1119">Se non viene specificata la porta:</span><span class="sxs-lookup"><span data-stu-id="72750-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="72750-1120">Il middleware usa come valore predefinito `null`.</span><span class="sxs-lookup"><span data-stu-id="72750-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="72750-1121">Lo schema viene modificato in `https` (protocollo HTTPS) e il client accede alla risorsa attraverso la porta 443.</span><span class="sxs-lookup"><span data-stu-id="72750-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="72750-1122">L'esempio seguente illustra come impostare il codice di stato su *301 - Spostato permanentemente* e come passare alla porta 5001.</span><span class="sxs-lookup"><span data-stu-id="72750-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="72750-1123">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> per reindirizzare le richieste non protette allo stesso host e allo stesso percorso con il protocollo protetto HTTPS attraverso la porta 443.</span><span class="sxs-lookup"><span data-stu-id="72750-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="72750-1124">Il middleware imposta il codice di stato su *301 - Spostato permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="72750-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="72750-1125">Per il reindirizzamento a un endpoint protetto quando non sono richieste regole di reindirizzamento aggiuntive, è consigliabile usare il middleware di reindirizzamento HTTPS.</span><span class="sxs-lookup"><span data-stu-id="72750-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="72750-1126">Per altre informazioni, vedere l'argomento [Imporre HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="72750-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="72750-1127">L'app di esempio indica come usare `AddRedirectToHttps` o `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="72750-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="72750-1128">Aggiungere il metodo di estensione a `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="72750-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="72750-1129">Eseguire una richiesta non sicura all'app su qualsiasi URL.</span><span class="sxs-lookup"><span data-stu-id="72750-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="72750-1130">Ignorare l'avviso di sicurezza del browser indicante che il certificato autofirmato non è attendibile oppure creare un'eccezione per rendere affidabile il certificato.</span><span class="sxs-lookup"><span data-stu-id="72750-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="72750-1131">Richiesta originale con `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="72750-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="72750-1133">Richiesta originale con `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="72750-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="72750-1135">Riscrittura URL</span><span class="sxs-lookup"><span data-stu-id="72750-1135">URL rewrite</span></span>

<span data-ttu-id="72750-1136">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> per creare una regola di riscrittura URL.</span><span class="sxs-lookup"><span data-stu-id="72750-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="72750-1137">Il primo parametro contiene l'espressione regolare per la corrispondenza in base al percorso dell'URL in ingresso.</span><span class="sxs-lookup"><span data-stu-id="72750-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="72750-1138">Il secondo parametro è la stringa di sostituzione.</span><span class="sxs-lookup"><span data-stu-id="72750-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="72750-1139">Il terzo parametro, `skipRemainingRules: {true|false}`, indica al middleware se ignorare le regole di riscrittura aggiuntive quando viene applicata la regola corrente.</span><span class="sxs-lookup"><span data-stu-id="72750-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="72750-1140">Richiesta originale: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="72750-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richiesta e risposta](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="72750-1142">L'accento circonflesso (`^`) all'inizio dell'espressione indica che la corrispondenza inizia all'inizio del percorso dell'URL.</span><span class="sxs-lookup"><span data-stu-id="72750-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="72750-1143">Nell'esempio precedente della regola di reindirizzamento `redirect-rule/(.*)`, non è presente l'accento circonflesso (`^`) all'inizio dell'espressione regolare.</span><span class="sxs-lookup"><span data-stu-id="72750-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="72750-1144">È quindi possibile ottenere una corrispondenza anteponendo qualsiasi carattere a `redirect-rule/`.</span><span class="sxs-lookup"><span data-stu-id="72750-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="72750-1145">Percorso</span><span class="sxs-lookup"><span data-stu-id="72750-1145">Path</span></span>                               | <span data-ttu-id="72750-1146">Corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-1146">Match</span></span> |
| ---
<span data-ttu-id="72750-1147">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1148">'Blazor'</span></span>
- <span data-ttu-id="72750-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1149">'Identity'</span></span>
- <span data-ttu-id="72750-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1151">'Razor'</span></span>
- <span data-ttu-id="72750-1152">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1153">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1154">'Blazor'</span></span>
- <span data-ttu-id="72750-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1155">'Identity'</span></span>
- <span data-ttu-id="72750-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1157">'Razor'</span></span>
- <span data-ttu-id="72750-1158">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1159">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1160">'Blazor'</span></span>
- <span data-ttu-id="72750-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1161">'Identity'</span></span>
- <span data-ttu-id="72750-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1163">'Razor'</span></span>
- <span data-ttu-id="72750-1164">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1165">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1166">'Blazor'</span></span>
- <span data-ttu-id="72750-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1167">'Identity'</span></span>
- <span data-ttu-id="72750-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1169">'Razor'</span></span>
- <span data-ttu-id="72750-1170">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1171">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1172">'Blazor'</span></span>
- <span data-ttu-id="72750-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1173">'Identity'</span></span>
- <span data-ttu-id="72750-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1175">'Razor'</span></span>
- <span data-ttu-id="72750-1176">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1177">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1178">'Blazor'</span></span>
- <span data-ttu-id="72750-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1179">'Identity'</span></span>
- <span data-ttu-id="72750-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1181">'Razor'</span></span>
- <span data-ttu-id="72750-1182">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1183">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1184">'Blazor'</span></span>
- <span data-ttu-id="72750-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1185">'Identity'</span></span>
- <span data-ttu-id="72750-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1187">'Razor'</span></span>
- <span data-ttu-id="72750-1188">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1189">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1190">'Blazor'</span></span>
- <span data-ttu-id="72750-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1191">'Identity'</span></span>
- <span data-ttu-id="72750-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1193">'Razor'</span></span>
- <span data-ttu-id="72750-1194">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1195">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1196">'Blazor'</span></span>
- <span data-ttu-id="72750-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1197">'Identity'</span></span>
- <span data-ttu-id="72750-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1199">'Razor'</span></span>
- <span data-ttu-id="72750-1200">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1201">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1202">'Blazor'</span></span>
- <span data-ttu-id="72750-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1203">'Identity'</span></span>
- <span data-ttu-id="72750-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1205">'Razor'</span></span>
- <span data-ttu-id="72750-1206">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1207">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1208">'Blazor'</span></span>
- <span data-ttu-id="72750-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1209">'Identity'</span></span>
- <span data-ttu-id="72750-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1211">'Razor'</span></span>
- <span data-ttu-id="72750-1212">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1213">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1214">'Blazor'</span></span>
- <span data-ttu-id="72750-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1215">'Identity'</span></span>
- <span data-ttu-id="72750-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1217">'Razor'</span></span>
- <span data-ttu-id="72750-1218">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1219">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1220">'Blazor'</span></span>
- <span data-ttu-id="72750-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1221">'Identity'</span></span>
- <span data-ttu-id="72750-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1223">'Razor'</span></span>
- <span data-ttu-id="72750-1224">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1225">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1226">'Blazor'</span></span>
- <span data-ttu-id="72750-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1227">'Identity'</span></span>
- <span data-ttu-id="72750-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1229">'Razor'</span></span>
- <span data-ttu-id="72750-1230">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1231">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1232">'Blazor'</span></span>
- <span data-ttu-id="72750-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1233">'Identity'</span></span>
- <span data-ttu-id="72750-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1235">'Razor'</span></span>
- <span data-ttu-id="72750-1236">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Sì | | `/my-cool-redirect-rule/1234/5678` | Sì | | `/anotherredirect-rule/1234/5678`  | Sì |</span><span class="sxs-lookup"><span data-stu-id="72750-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="72750-1238">La regola di riscrittura, `^rewrite-rule/(\d+)/(\d+)`, rileva la corrispondenza dei percorsi solo se iniziano con `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="72750-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="72750-1239">Nella tabella seguente, si noti la differenza nella corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="72750-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="72750-1240">Percorso</span><span class="sxs-lookup"><span data-stu-id="72750-1240">Path</span></span>                              | <span data-ttu-id="72750-1241">Corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-1241">Match</span></span> |
| ---
<span data-ttu-id="72750-1242">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1243">'Blazor'</span></span>
- <span data-ttu-id="72750-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1244">'Identity'</span></span>
- <span data-ttu-id="72750-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1246">'Razor'</span></span>
- <span data-ttu-id="72750-1247">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1248">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1249">'Blazor'</span></span>
- <span data-ttu-id="72750-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1250">'Identity'</span></span>
- <span data-ttu-id="72750-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1252">'Razor'</span></span>
- <span data-ttu-id="72750-1253">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1254">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1255">'Blazor'</span></span>
- <span data-ttu-id="72750-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1256">'Identity'</span></span>
- <span data-ttu-id="72750-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1258">'Razor'</span></span>
- <span data-ttu-id="72750-1259">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1260">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1261">'Blazor'</span></span>
- <span data-ttu-id="72750-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1262">'Identity'</span></span>
- <span data-ttu-id="72750-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1264">'Razor'</span></span>
- <span data-ttu-id="72750-1265">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1266">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1267">'Blazor'</span></span>
- <span data-ttu-id="72750-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1268">'Identity'</span></span>
- <span data-ttu-id="72750-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1270">'Razor'</span></span>
- <span data-ttu-id="72750-1271">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1272">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1273">'Blazor'</span></span>
- <span data-ttu-id="72750-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1274">'Identity'</span></span>
- <span data-ttu-id="72750-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1276">'Razor'</span></span>
- <span data-ttu-id="72750-1277">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1278">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1279">'Blazor'</span></span>
- <span data-ttu-id="72750-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1280">'Identity'</span></span>
- <span data-ttu-id="72750-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1282">'Razor'</span></span>
- <span data-ttu-id="72750-1283">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1284">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1285">'Blazor'</span></span>
- <span data-ttu-id="72750-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1286">'Identity'</span></span>
- <span data-ttu-id="72750-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1288">'Razor'</span></span>
- <span data-ttu-id="72750-1289">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1290">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1291">'Blazor'</span></span>
- <span data-ttu-id="72750-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1292">'Identity'</span></span>
- <span data-ttu-id="72750-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1294">'Razor'</span></span>
- <span data-ttu-id="72750-1295">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1296">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1297">'Blazor'</span></span>
- <span data-ttu-id="72750-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1298">'Identity'</span></span>
- <span data-ttu-id="72750-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1300">'Razor'</span></span>
- <span data-ttu-id="72750-1301">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1302">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1303">'Blazor'</span></span>
- <span data-ttu-id="72750-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1304">'Identity'</span></span>
- <span data-ttu-id="72750-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1306">'Razor'</span></span>
- <span data-ttu-id="72750-1307">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1308">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1309">'Blazor'</span></span>
- <span data-ttu-id="72750-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1310">'Identity'</span></span>
- <span data-ttu-id="72750-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1312">'Razor'</span></span>
- <span data-ttu-id="72750-1313">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1314">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1315">'Blazor'</span></span>
- <span data-ttu-id="72750-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1316">'Identity'</span></span>
- <span data-ttu-id="72750-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1318">'Razor'</span></span>
- <span data-ttu-id="72750-1319">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1320">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1321">'Blazor'</span></span>
- <span data-ttu-id="72750-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1322">'Identity'</span></span>
- <span data-ttu-id="72750-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1324">'Razor'</span></span>
- <span data-ttu-id="72750-1325">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Sì | | `/my-cool-rewrite-rule/1234/5678` | No | | `/anotherrewrite-rule/1234/5678`  | No |</span><span class="sxs-lookup"><span data-stu-id="72750-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="72750-1327">Dopo la parte `^rewrite-rule/` dell'espressione sono presenti due gruppi Capture, `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="72750-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="72750-1328">`\d` indica *corrispondenza con una cifra (numero)*.</span><span class="sxs-lookup"><span data-stu-id="72750-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="72750-1329">Il segno più (`+`) indica *corrispondenza con uno o più dei caratteri precedenti*.</span><span class="sxs-lookup"><span data-stu-id="72750-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="72750-1330">Di conseguenza l'URL deve contenere un numero seguito da una barra seguita a sua volta da un altro numero.</span><span class="sxs-lookup"><span data-stu-id="72750-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="72750-1331">Questi gruppi Capture vengono inseriti nell'URL riscritto come `$1` e `$2`.</span><span class="sxs-lookup"><span data-stu-id="72750-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="72750-1332">La stringa di sostituzione della regola di riscrittura inserisce i gruppi acquisiti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="72750-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="72750-1333">Il percorso richiesto `/rewrite-rule/1234/5678` viene riscritto per ottenere la risorsa in `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="72750-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="72750-1334">Se nella richiesta originale è presente una stringa di query, la stringa viene mantenuta quando l'URL viene riscritto.</span><span class="sxs-lookup"><span data-stu-id="72750-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="72750-1335">Non viene eseguito alcun round trip al server per ottenere la risorsa.</span><span class="sxs-lookup"><span data-stu-id="72750-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="72750-1336">Se la risorsa esiste, viene recuperata e restituita al client con il codice di stato *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="72750-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="72750-1337">Poiché il client non è reindirizzato, l'URL nella barra degli indirizzi del browser non cambia.</span><span class="sxs-lookup"><span data-stu-id="72750-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="72750-1338">I client non sono in grado di rilevare che si è verificata un'operazione di riscrittura URL nel server.</span><span class="sxs-lookup"><span data-stu-id="72750-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="72750-1339">Se possibile, usare sempre `skipRemainingRules: true`, perché la corrispondenza tra le regole è onerosa dal punto di vista del calcolo e aumenta il tempo di risposta dell'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="72750-1340">Per velocizzare il tempo di risposta dell'app:</span><span class="sxs-lookup"><span data-stu-id="72750-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="72750-1341">Ordinare le regole di riscrittura dalla regola che origina più corrispondenze a quella che origina meno corrispondenze.</span><span class="sxs-lookup"><span data-stu-id="72750-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="72750-1342">Quando viene rilevata una corrispondenza e non sono necessarie altre operazioni di elaborazione delle regole, ignorare l'elaborazione delle regole rimanenti.</span><span class="sxs-lookup"><span data-stu-id="72750-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="72750-1343">Modulo Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="72750-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="72750-1344">Applicare le regole del modulo Apache mod_rewrite con <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="72750-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="72750-1345">Assicurarsi che il file delle regole venga distribuito con l'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="72750-1346">Per altre informazioni ed esempi di regole mod_rewrite, vedere [Modulo Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="72750-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="72750-1347"><xref:System.IO.StreamReader>Viene usato per leggere le regole del file di regole *ApacheModRewrite. txt* :</span><span class="sxs-lookup"><span data-stu-id="72750-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="72750-1348">L'app di esempio reindirizza le richieste da `/apache-mod-rules-redirect/(.\*)` a `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="72750-1349">Il codice di stato della risposta è *302 - Trovato*.</span><span class="sxs-lookup"><span data-stu-id="72750-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="72750-1350">Richiesta originale: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="72750-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="72750-1352">Il middleware supporta le seguenti variabili server del modulo Apache mod_rewrite:</span><span class="sxs-lookup"><span data-stu-id="72750-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="72750-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="72750-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="72750-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="72750-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="72750-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="72750-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="72750-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="72750-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="72750-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="72750-1358">HTTP_HOST</span></span>
* <span data-ttu-id="72750-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="72750-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="72750-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="72750-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="72750-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="72750-1361">HTTPS</span></span>
* <span data-ttu-id="72750-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="72750-1362">IPV6</span></span>
* <span data-ttu-id="72750-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="72750-1363">QUERY_STRING</span></span>
* <span data-ttu-id="72750-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="72750-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="72750-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="72750-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="72750-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="72750-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="72750-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="72750-1369">REQUEST_URI</span></span>
* <span data-ttu-id="72750-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="72750-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="72750-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-1372">SERVER_PORT</span></span>
* <span data-ttu-id="72750-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="72750-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="72750-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="72750-1374">TIME</span></span>
* <span data-ttu-id="72750-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="72750-1375">TIME_DAY</span></span>
* <span data-ttu-id="72750-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="72750-1376">TIME_HOUR</span></span>
* <span data-ttu-id="72750-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="72750-1377">TIME_MIN</span></span>
* <span data-ttu-id="72750-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="72750-1378">TIME_MON</span></span>
* <span data-ttu-id="72750-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="72750-1379">TIME_SEC</span></span>
* <span data-ttu-id="72750-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="72750-1380">TIME_WDAY</span></span>
* <span data-ttu-id="72750-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="72750-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="72750-1382">Regole di IIS URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="72750-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="72750-1383">Per usare lo stesso set di regole applicabile a IIS URL Rewrite Module, usare <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="72750-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="72750-1384">Assicurarsi che il file delle regole venga distribuito con l'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="72750-1385">Non indirizzare il middleware all'uso del file *web.config* dell'app quando è in esecuzione in Windows Server IIS.</span><span class="sxs-lookup"><span data-stu-id="72750-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="72750-1386">Con IIS queste regole devono essere archiviate al di fuori del file *web.config* dell'app, per evitare conflitti con il modulo IIS Rewrite.</span><span class="sxs-lookup"><span data-stu-id="72750-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="72750-1387">Per altre informazioni ed esempi di regole di IIS URL Rewrite Module, vedere [Using URL Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso di URL Rewrite Module 2.0) e [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Guida di riferimento per la configurazione di URL Rewrite Module).</span><span class="sxs-lookup"><span data-stu-id="72750-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="72750-1388"><xref:System.IO.StreamReader>Viene usato per leggere le regole del file di regole *IISUrlRewrite. XML* :</span><span class="sxs-lookup"><span data-stu-id="72750-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="72750-1389">L'app di esempio riscrive le richieste da `/iis-rules-rewrite/(.*)` a `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="72750-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="72750-1390">La risposta viene inviata al client con il codice di stato *200 - OK*.</span><span class="sxs-lookup"><span data-stu-id="72750-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="72750-1391">Richiesta originale: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="72750-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richiesta e risposta](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="72750-1393">Se è presente un modulo IIS Rewrite attivo con regole di livello server configurate che possono avere effetti indesiderati nell'app, è possibile disabilitare il modulo IIS Rewrite per un'app.</span><span class="sxs-lookup"><span data-stu-id="72750-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="72750-1394">Per altre informazioni, vedere [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules) (Disabilitazione di moduli IIS).</span><span class="sxs-lookup"><span data-stu-id="72750-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="72750-1395">Funzionalità non supportate</span><span class="sxs-lookup"><span data-stu-id="72750-1395">Unsupported features</span></span>

<span data-ttu-id="72750-1396">Il middleware rilasciato con ASP.NET Core 2.x non supporta le seguenti funzionalità di IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="72750-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="72750-1397">Regole in uscita</span><span class="sxs-lookup"><span data-stu-id="72750-1397">Outbound Rules</span></span>
* <span data-ttu-id="72750-1398">Variabili server personalizzate</span><span class="sxs-lookup"><span data-stu-id="72750-1398">Custom Server Variables</span></span>
* <span data-ttu-id="72750-1399">Caratteri jolly</span><span class="sxs-lookup"><span data-stu-id="72750-1399">Wildcards</span></span>
* <span data-ttu-id="72750-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="72750-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="72750-1401">Variabili server supportate</span><span class="sxs-lookup"><span data-stu-id="72750-1401">Supported server variables</span></span>

<span data-ttu-id="72750-1402">Il middleware supporta le seguenti variabili server di IIS URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="72750-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="72750-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="72750-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="72750-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="72750-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="72750-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="72750-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="72750-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="72750-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="72750-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="72750-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="72750-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="72750-1408">HTTP_HOST</span></span>
* <span data-ttu-id="72750-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="72750-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="72750-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="72750-1410">HTTP_URL</span></span>
* <span data-ttu-id="72750-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="72750-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="72750-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="72750-1412">HTTPS</span></span>
* <span data-ttu-id="72750-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="72750-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="72750-1414">QUERY_STRING</span></span>
* <span data-ttu-id="72750-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="72750-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="72750-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="72750-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="72750-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="72750-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="72750-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="72750-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="72750-1419">È anche possibile ottenere un <xref:Microsoft.Extensions.FileProviders.IFileProvider> tramite un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="72750-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="72750-1420">Questo approccio può garantire maggior flessibilità per la posizione dei file delle regole di riscrittura.</span><span class="sxs-lookup"><span data-stu-id="72750-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="72750-1421">Assicurarsi che i file di regole di riscrittura vengano distribuiti nel server in corrispondenza del percorso specificato.</span><span class="sxs-lookup"><span data-stu-id="72750-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="72750-1422">Regola basata su metodo</span><span class="sxs-lookup"><span data-stu-id="72750-1422">Method-based rule</span></span>

<span data-ttu-id="72750-1423">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> per implementare logica della regola personalizzata in un metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="72750-1424">`Add` espone <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, che rende disponibile <xref:Microsoft.AspNetCore.Http.HttpContext> per l'uso nel metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="72750-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determina la modalità di gestione dell'elaborazione pipeline aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="72750-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="72750-1426">Impostare il valore su uno dei campi <xref:Microsoft.AspNetCore.Rewrite.RuleResult> descritti nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="72750-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="72750-1427">Azione</span><span class="sxs-lookup"><span data-stu-id="72750-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="72750-1428">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1429">'Blazor'</span></span>
- <span data-ttu-id="72750-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1430">'Identity'</span></span>
- <span data-ttu-id="72750-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1432">'Razor'</span></span>
- <span data-ttu-id="72750-1433">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1434">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1435">'Blazor'</span></span>
- <span data-ttu-id="72750-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1436">'Identity'</span></span>
- <span data-ttu-id="72750-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1438">'Razor'</span></span>
- <span data-ttu-id="72750-1439">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1440">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1441">'Blazor'</span></span>
- <span data-ttu-id="72750-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1442">'Identity'</span></span>
- <span data-ttu-id="72750-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1444">'Razor'</span></span>
- <span data-ttu-id="72750-1445">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1446">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1447">'Blazor'</span></span>
- <span data-ttu-id="72750-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1448">'Identity'</span></span>
- <span data-ttu-id="72750-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1450">'Razor'</span></span>
- <span data-ttu-id="72750-1451">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1452">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1453">'Blazor'</span></span>
- <span data-ttu-id="72750-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1454">'Identity'</span></span>
- <span data-ttu-id="72750-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1456">'Razor'</span></span>
- <span data-ttu-id="72750-1457">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1458">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1459">'Blazor'</span></span>
- <span data-ttu-id="72750-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1460">'Identity'</span></span>
- <span data-ttu-id="72750-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1462">'Razor'</span></span>
- <span data-ttu-id="72750-1463">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1464">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1465">'Blazor'</span></span>
- <span data-ttu-id="72750-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1466">'Identity'</span></span>
- <span data-ttu-id="72750-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1468">'Razor'</span></span>
- <span data-ttu-id="72750-1469">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1470">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1471">'Blazor'</span></span>
- <span data-ttu-id="72750-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1472">'Identity'</span></span>
- <span data-ttu-id="72750-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1474">'Razor'</span></span>
- <span data-ttu-id="72750-1475">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1476">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1477">'Blazor'</span></span>
- <span data-ttu-id="72750-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1478">'Identity'</span></span>
- <span data-ttu-id="72750-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1480">'Razor'</span></span>
- <span data-ttu-id="72750-1481">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1482">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1483">'Blazor'</span></span>
- <span data-ttu-id="72750-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1484">'Identity'</span></span>
- <span data-ttu-id="72750-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1486">'Razor'</span></span>
- <span data-ttu-id="72750-1487">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1488">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1489">'Blazor'</span></span>
- <span data-ttu-id="72750-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1490">'Identity'</span></span>
- <span data-ttu-id="72750-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1492">'Razor'</span></span>
- <span data-ttu-id="72750-1493">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1494">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1495">'Blazor'</span></span>
- <span data-ttu-id="72750-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1496">'Identity'</span></span>
- <span data-ttu-id="72750-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1498">'Razor'</span></span>
- <span data-ttu-id="72750-1499">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1500">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1501">'Blazor'</span></span>
- <span data-ttu-id="72750-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1502">'Identity'</span></span>
- <span data-ttu-id="72750-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1504">'Razor'</span></span>
- <span data-ttu-id="72750-1505">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1506">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1507">'Blazor'</span></span>
- <span data-ttu-id="72750-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1508">'Identity'</span></span>
- <span data-ttu-id="72750-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1510">'Razor'</span></span>
- <span data-ttu-id="72750-1511">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1512">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1513">'Blazor'</span></span>
- <span data-ttu-id="72750-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1514">'Identity'</span></span>
- <span data-ttu-id="72750-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1516">'Razor'</span></span>
- <span data-ttu-id="72750-1517">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1518">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1519">'Blazor'</span></span>
- <span data-ttu-id="72750-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1520">'Identity'</span></span>
- <span data-ttu-id="72750-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1522">'Razor'</span></span>
- <span data-ttu-id="72750-1523">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1524">------------------ | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1525">'Blazor'</span></span>
- <span data-ttu-id="72750-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1526">'Identity'</span></span>
- <span data-ttu-id="72750-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1528">'Razor'</span></span>
- <span data-ttu-id="72750-1529">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1530">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1531">'Blazor'</span></span>
- <span data-ttu-id="72750-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1532">'Identity'</span></span>
- <span data-ttu-id="72750-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1534">'Razor'</span></span>
- <span data-ttu-id="72750-1535">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1536">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1537">'Blazor'</span></span>
- <span data-ttu-id="72750-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1538">'Identity'</span></span>
- <span data-ttu-id="72750-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1540">'Razor'</span></span>
- <span data-ttu-id="72750-1541">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1542">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1543">'Blazor'</span></span>
- <span data-ttu-id="72750-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1544">'Identity'</span></span>
- <span data-ttu-id="72750-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1546">'Razor'</span></span>
- <span data-ttu-id="72750-1547">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1548">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1549">'Blazor'</span></span>
- <span data-ttu-id="72750-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1550">'Identity'</span></span>
- <span data-ttu-id="72750-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1552">'Razor'</span></span>
- <span data-ttu-id="72750-1553">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1554">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1555">'Blazor'</span></span>
- <span data-ttu-id="72750-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1556">'Identity'</span></span>
- <span data-ttu-id="72750-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1558">'Razor'</span></span>
- <span data-ttu-id="72750-1559">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1560">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1561">'Blazor'</span></span>
- <span data-ttu-id="72750-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1562">'Identity'</span></span>
- <span data-ttu-id="72750-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1564">'Razor'</span></span>
- <span data-ttu-id="72750-1565">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1566">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1567">'Blazor'</span></span>
- <span data-ttu-id="72750-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1568">'Identity'</span></span>
- <span data-ttu-id="72750-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1570">'Razor'</span></span>
- <span data-ttu-id="72750-1571">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1572">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1573">'Blazor'</span></span>
- <span data-ttu-id="72750-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1574">'Identity'</span></span>
- <span data-ttu-id="72750-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1576">'Razor'</span></span>
- <span data-ttu-id="72750-1577">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1578">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1579">'Blazor'</span></span>
- <span data-ttu-id="72750-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1580">'Identity'</span></span>
- <span data-ttu-id="72750-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1582">'Razor'</span></span>
- <span data-ttu-id="72750-1583">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1584">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1585">'Blazor'</span></span>
- <span data-ttu-id="72750-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1586">'Identity'</span></span>
- <span data-ttu-id="72750-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1588">'Razor'</span></span>
- <span data-ttu-id="72750-1589">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1590">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1591">'Blazor'</span></span>
- <span data-ttu-id="72750-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1592">'Identity'</span></span>
- <span data-ttu-id="72750-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1594">'Razor'</span></span>
- <span data-ttu-id="72750-1595">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1596">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1597">'Blazor'</span></span>
- <span data-ttu-id="72750-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1598">'Identity'</span></span>
- <span data-ttu-id="72750-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1600">'Razor'</span></span>
- <span data-ttu-id="72750-1601">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1602">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1603">'Blazor'</span></span>
- <span data-ttu-id="72750-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1604">'Identity'</span></span>
- <span data-ttu-id="72750-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1606">'Razor'</span></span>
- <span data-ttu-id="72750-1607">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1608">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1609">'Blazor'</span></span>
- <span data-ttu-id="72750-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1610">'Identity'</span></span>
- <span data-ttu-id="72750-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1612">'Razor'</span></span>
- <span data-ttu-id="72750-1613">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1614">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1615">'Blazor'</span></span>
- <span data-ttu-id="72750-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1616">'Identity'</span></span>
- <span data-ttu-id="72750-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1618">'Razor'</span></span>
- <span data-ttu-id="72750-1619">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1620">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1621">'Blazor'</span></span>
- <span data-ttu-id="72750-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1622">'Identity'</span></span>
- <span data-ttu-id="72750-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1624">'Razor'</span></span>
- <span data-ttu-id="72750-1625">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1626">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1627">'Blazor'</span></span>
- <span data-ttu-id="72750-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1628">'Identity'</span></span>
- <span data-ttu-id="72750-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1630">'Razor'</span></span>
- <span data-ttu-id="72750-1631">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1632">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1633">'Blazor'</span></span>
- <span data-ttu-id="72750-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1634">'Identity'</span></span>
- <span data-ttu-id="72750-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1636">'Razor'</span></span>
- <span data-ttu-id="72750-1637">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1638">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1639">'Blazor'</span></span>
- <span data-ttu-id="72750-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1640">'Identity'</span></span>
- <span data-ttu-id="72750-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1642">'Razor'</span></span>
- <span data-ttu-id="72750-1643">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1644">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1645">'Blazor'</span></span>
- <span data-ttu-id="72750-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1646">'Identity'</span></span>
- <span data-ttu-id="72750-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1648">'Razor'</span></span>
- <span data-ttu-id="72750-1649">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1650">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1651">'Blazor'</span></span>
- <span data-ttu-id="72750-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1652">'Identity'</span></span>
- <span data-ttu-id="72750-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1654">'Razor'</span></span>
- <span data-ttu-id="72750-1655">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1656">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1657">'Blazor'</span></span>
- <span data-ttu-id="72750-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1658">'Identity'</span></span>
- <span data-ttu-id="72750-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1660">'Razor'</span></span>
- <span data-ttu-id="72750-1661">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1662">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1663">'Blazor'</span></span>
- <span data-ttu-id="72750-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1664">'Identity'</span></span>
- <span data-ttu-id="72750-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1666">'Razor'</span></span>
- <span data-ttu-id="72750-1667">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1668">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1669">'Blazor'</span></span>
- <span data-ttu-id="72750-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1670">'Identity'</span></span>
- <span data-ttu-id="72750-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1672">'Razor'</span></span>
- <span data-ttu-id="72750-1673">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1674">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1675">'Blazor'</span></span>
- <span data-ttu-id="72750-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1676">'Identity'</span></span>
- <span data-ttu-id="72750-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1678">'Razor'</span></span>
- <span data-ttu-id="72750-1679">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1680">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1681">'Blazor'</span></span>
- <span data-ttu-id="72750-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1682">'Identity'</span></span>
- <span data-ttu-id="72750-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1684">'Razor'</span></span>
- <span data-ttu-id="72750-1685">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1686">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1687">'Blazor'</span></span>
- <span data-ttu-id="72750-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1688">'Identity'</span></span>
- <span data-ttu-id="72750-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1690">'Razor'</span></span>
- <span data-ttu-id="72750-1691">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1692">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1693">'Blazor'</span></span>
- <span data-ttu-id="72750-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1694">'Identity'</span></span>
- <span data-ttu-id="72750-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1696">'Razor'</span></span>
- <span data-ttu-id="72750-1697">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1698">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1699">'Blazor'</span></span>
- <span data-ttu-id="72750-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1700">'Identity'</span></span>
- <span data-ttu-id="72750-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1702">'Razor'</span></span>
- <span data-ttu-id="72750-1703">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1704">-------------------------------- | | `RuleResult.ContinueRules`(impostazione predefinita) | Continuare a applicare le regole.</span><span class="sxs-lookup"><span data-stu-id="72750-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="72750-1705">| | `RuleResult.EndResponse`             | Interrompere l'applicazione delle regole e inviare la risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="72750-1706">| | `RuleResult.SkipRemainingRules`      | Interrompere l'applicazione delle regole e inviare il contesto al middleware successivo.</span><span class="sxs-lookup"><span data-stu-id="72750-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="72750-1707">L'app di esempio visualizza un metodo che reindirizza le richieste per i percorsi che terminano con *.xml*.</span><span class="sxs-lookup"><span data-stu-id="72750-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="72750-1708">Se viene inviata una richiesta per `/file.xml`, la richiesta viene reindirizzata a `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="72750-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="72750-1709">Il codice di stato viene impostato su *301 - Spostato permanentemente*.</span><span class="sxs-lookup"><span data-stu-id="72750-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="72750-1710">Quando il browser invia una nuova richiesta per */xmlfiles/file.xml*, il middleware dei file statici rende disponibile il file al client dalla cartella *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="72750-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="72750-1711">Per un reindirizzamento, impostare in modo esplicito il codice di stato della risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="72750-1712">In caso contrario, viene restituito il codice di stato *200 - OK* e il reindirizzamento non viene eseguito nel client.</span><span class="sxs-lookup"><span data-stu-id="72750-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="72750-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="72750-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="72750-1714">Questo approccio consente anche di riscrivere le richieste.</span><span class="sxs-lookup"><span data-stu-id="72750-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="72750-1715">L'app di esempio illustra la riscrittura del percorso di una richiesta di file di testo per rendere disponibile il file di testo *file. txt* dalla cartella *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="72750-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="72750-1716">Il middleware dei file statici rende disponibile il file in base al percorso di richiesta aggiornato:</span><span class="sxs-lookup"><span data-stu-id="72750-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="72750-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="72750-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="72750-1718">Regola basata su IRule</span><span class="sxs-lookup"><span data-stu-id="72750-1718">IRule-based rule</span></span>

<span data-ttu-id="72750-1719">Usare <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> per usare la logica della regola in una classe che implementa l'interfaccia <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="72750-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="72750-1720">`IRule` garantisce maggiore flessibilità rispetto all'approccio con una regola basata su un metodo.</span><span class="sxs-lookup"><span data-stu-id="72750-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="72750-1721">La classe di implementazione può includere un costruttore, che consente di passare parametri per il metodo <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="72750-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="72750-1722">I valori dei parametri nell'app di esempio per `extension` e `newPath` vengono verificati e devono soddisfare diverse condizioni.</span><span class="sxs-lookup"><span data-stu-id="72750-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="72750-1723">`extension` deve contenere un valore e tale valore deve essere *.png*, *.jpg* o *.gif*.</span><span class="sxs-lookup"><span data-stu-id="72750-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="72750-1724">Se `newPath` non è valido, viene generato un evento <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="72750-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="72750-1725">Se viene inviata una richiesta per *image.png*, la richiesta viene reindirizzata a `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="72750-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="72750-1726">Se viene inviata una richiesta per *image.jpg*, la richiesta viene reindirizzata a `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="72750-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="72750-1727">Il codice di stato viene impostato su *301 - Spostato permanentemente* e l'elemento `context.Result` viene impostato per l'interruzione dell'elaborazione delle regole e l'invio della risposta.</span><span class="sxs-lookup"><span data-stu-id="72750-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="72750-1728">Richiesta originale: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="72750-1728">Original Request: `/image.png`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte per image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="72750-1730">Richiesta originale: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="72750-1730">Original Request: `/image.jpg`</span></span>

![Finestra del browser con gli strumenti di sviluppo per il rilevamento di richieste e risposte per image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="72750-1732">Esempi di espressioni regolari</span><span class="sxs-lookup"><span data-stu-id="72750-1732">Regex examples</span></span>

| <span data-ttu-id="72750-1733">Obiettivo</span><span class="sxs-lookup"><span data-stu-id="72750-1733">Goal</span></span> | <span data-ttu-id="72750-1734">Esempio di stringa espressione regolare</span><span class="sxs-lookup"><span data-stu-id="72750-1734">Regex String &</span></span><br><span data-ttu-id="72750-1735">e corrispondenza</span><span class="sxs-lookup"><span data-stu-id="72750-1735">Match Example</span></span> | <span data-ttu-id="72750-1736">Esempio di stringa di sostituzione</span><span class="sxs-lookup"><span data-stu-id="72750-1736">Replacement String &</span></span><br><span data-ttu-id="72750-1737">e output</span><span class="sxs-lookup"><span data-stu-id="72750-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="72750-1738">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1739">'Blazor'</span></span>
- <span data-ttu-id="72750-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1740">'Identity'</span></span>
- <span data-ttu-id="72750-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1742">'Razor'</span></span>
- <span data-ttu-id="72750-1743">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1744">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1745">'Blazor'</span></span>
- <span data-ttu-id="72750-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1746">'Identity'</span></span>
- <span data-ttu-id="72750-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1748">'Razor'</span></span>
- <span data-ttu-id="72750-1749">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1750">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1751">'Blazor'</span></span>
- <span data-ttu-id="72750-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1752">'Identity'</span></span>
- <span data-ttu-id="72750-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1754">'Razor'</span></span>
- <span data-ttu-id="72750-1755">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1756">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1757">'Blazor'</span></span>
- <span data-ttu-id="72750-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1758">'Identity'</span></span>
- <span data-ttu-id="72750-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1760">'Razor'</span></span>
- <span data-ttu-id="72750-1761">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1762">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1763">'Blazor'</span></span>
- <span data-ttu-id="72750-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1764">'Identity'</span></span>
- <span data-ttu-id="72750-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1766">'Razor'</span></span>
- <span data-ttu-id="72750-1767">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1768">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1769">'Blazor'</span></span>
- <span data-ttu-id="72750-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1770">'Identity'</span></span>
- <span data-ttu-id="72750-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1772">'Razor'</span></span>
- <span data-ttu-id="72750-1773">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1774">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1775">'Blazor'</span></span>
- <span data-ttu-id="72750-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1776">'Identity'</span></span>
- <span data-ttu-id="72750-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1778">'Razor'</span></span>
- <span data-ttu-id="72750-1779">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1780">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1781">'Blazor'</span></span>
- <span data-ttu-id="72750-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1782">'Identity'</span></span>
- <span data-ttu-id="72750-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1784">'Razor'</span></span>
- <span data-ttu-id="72750-1785">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1786">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1787">'Blazor'</span></span>
- <span data-ttu-id="72750-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1788">'Identity'</span></span>
- <span data-ttu-id="72750-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1790">'Razor'</span></span>
- <span data-ttu-id="72750-1791">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1792">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1793">'Blazor'</span></span>
- <span data-ttu-id="72750-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1794">'Identity'</span></span>
- <span data-ttu-id="72750-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1796">'Razor'</span></span>
- <span data-ttu-id="72750-1797">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1798">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1799">'Blazor'</span></span>
- <span data-ttu-id="72750-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1800">'Identity'</span></span>
- <span data-ttu-id="72750-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1802">'Razor'</span></span>
- <span data-ttu-id="72750-1803">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1804">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1805">'Blazor'</span></span>
- <span data-ttu-id="72750-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1806">'Identity'</span></span>
- <span data-ttu-id="72750-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1808">'Razor'</span></span>
- <span data-ttu-id="72750-1809">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1810">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1811">'Blazor'</span></span>
- <span data-ttu-id="72750-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1812">'Identity'</span></span>
- <span data-ttu-id="72750-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1814">'Razor'</span></span>
- <span data-ttu-id="72750-1815">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1816">---------------- | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1817">'Blazor'</span></span>
- <span data-ttu-id="72750-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1818">'Identity'</span></span>
- <span data-ttu-id="72750-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1820">'Razor'</span></span>
- <span data-ttu-id="72750-1821">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1822">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1823">'Blazor'</span></span>
- <span data-ttu-id="72750-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1824">'Identity'</span></span>
- <span data-ttu-id="72750-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1826">'Razor'</span></span>
- <span data-ttu-id="72750-1827">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1828">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1829">'Blazor'</span></span>
- <span data-ttu-id="72750-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1830">'Identity'</span></span>
- <span data-ttu-id="72750-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1832">'Razor'</span></span>
- <span data-ttu-id="72750-1833">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1834">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1835">'Blazor'</span></span>
- <span data-ttu-id="72750-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1836">'Identity'</span></span>
- <span data-ttu-id="72750-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1838">'Razor'</span></span>
- <span data-ttu-id="72750-1839">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1840">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1841">'Blazor'</span></span>
- <span data-ttu-id="72750-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1842">'Identity'</span></span>
- <span data-ttu-id="72750-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1844">'Razor'</span></span>
- <span data-ttu-id="72750-1845">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1846">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1847">'Blazor'</span></span>
- <span data-ttu-id="72750-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1848">'Identity'</span></span>
- <span data-ttu-id="72750-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1850">'Razor'</span></span>
- <span data-ttu-id="72750-1851">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1852">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1853">'Blazor'</span></span>
- <span data-ttu-id="72750-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1854">'Identity'</span></span>
- <span data-ttu-id="72750-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1856">'Razor'</span></span>
- <span data-ttu-id="72750-1857">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1858">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1859">'Blazor'</span></span>
- <span data-ttu-id="72750-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1860">'Identity'</span></span>
- <span data-ttu-id="72750-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1862">'Razor'</span></span>
- <span data-ttu-id="72750-1863">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1864">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1865">'Blazor'</span></span>
- <span data-ttu-id="72750-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1866">'Identity'</span></span>
- <span data-ttu-id="72750-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1868">'Razor'</span></span>
- <span data-ttu-id="72750-1869">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1870">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1871">'Blazor'</span></span>
- <span data-ttu-id="72750-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1872">'Identity'</span></span>
- <span data-ttu-id="72750-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1874">'Razor'</span></span>
- <span data-ttu-id="72750-1875">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1876">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1877">'Blazor'</span></span>
- <span data-ttu-id="72750-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1878">'Identity'</span></span>
- <span data-ttu-id="72750-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1880">'Razor'</span></span>
- <span data-ttu-id="72750-1881">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1882">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1883">'Blazor'</span></span>
- <span data-ttu-id="72750-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1884">'Identity'</span></span>
- <span data-ttu-id="72750-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1886">'Razor'</span></span>
- <span data-ttu-id="72750-1887">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1888">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1889">'Blazor'</span></span>
- <span data-ttu-id="72750-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1890">'Identity'</span></span>
- <span data-ttu-id="72750-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1892">'Razor'</span></span>
- <span data-ttu-id="72750-1893">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1894">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1895">'Blazor'</span></span>
- <span data-ttu-id="72750-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1896">'Identity'</span></span>
- <span data-ttu-id="72750-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1898">'Razor'</span></span>
- <span data-ttu-id="72750-1899">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1900">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1901">'Blazor'</span></span>
- <span data-ttu-id="72750-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1902">'Identity'</span></span>
- <span data-ttu-id="72750-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1904">'Razor'</span></span>
- <span data-ttu-id="72750-1905">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1906">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1907">'Blazor'</span></span>
- <span data-ttu-id="72750-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1908">'Identity'</span></span>
- <span data-ttu-id="72750-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1910">'Razor'</span></span>
- <span data-ttu-id="72750-1911">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="72750-1912">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="72750-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="72750-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="72750-1913">'Blazor'</span></span>
- <span data-ttu-id="72750-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="72750-1914">'Identity'</span></span>
- <span data-ttu-id="72750-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="72750-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="72750-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="72750-1916">'Razor'</span></span>
- <span data-ttu-id="72750-1917">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="72750-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="72750-1918">------------------- | | Riscrivere il percorso in QueryString |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="72750-1919">`/path?var1=abc&var2=123`| | Barra finale della striscia |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="72750-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="72750-1920">`/path`| | Imponi barra finale |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="72750-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="72750-1921">`/path/`| | Evitare la riscrittura di richieste specifiche | `^(.*)(?<!\.axd)$`o`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="72750-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="72750-1922">Sì: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="72750-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="72750-1923">No:`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="72750-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="72750-1924">`/resource.axd`| | Ridisporre i segmenti di URL |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="72750-1925">`path/3/2/1`| | Sostituire un segmento URL |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="72750-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="72750-1926">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="72750-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="72750-1927">Espressioni regolari in .NET</span><span class="sxs-lookup"><span data-stu-id="72750-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="72750-1928">Linguaggio di espressioni regolari-riferimento rapido</span><span class="sxs-lookup"><span data-stu-id="72750-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="72750-1929">Modulo Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="72750-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* <span data-ttu-id="72750-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) (Uso di URL Rewrite Module 2.0 per IIS)</span><span class="sxs-lookup"><span data-stu-id="72750-1930">[Using Url Rewrite Module 2.0 (for IIS)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)</span></span>
* <span data-ttu-id="72750-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference) (Guida di riferimento per la configurazione di URL Rewrite Module)</span><span class="sxs-lookup"><span data-stu-id="72750-1931">[URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span></span>
* [<span data-ttu-id="72750-1932">Forum di IIS URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="72750-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* <span data-ttu-id="72750-1933">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en) (Mantenere una struttura URL semplice)</span><span class="sxs-lookup"><span data-stu-id="72750-1933">[Keep a simple URL structure](https://support.google.com/webmasters/answer/76329?hl=en)</span></span>
* <span data-ttu-id="72750-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/) (10 suggerimenti e consigli per la riscrittura URL)</span><span class="sxs-lookup"><span data-stu-id="72750-1934">[10 URL Rewriting Tips and Tricks](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)</span></span>
* <span data-ttu-id="72750-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html) (Uso del carattere barra)</span><span class="sxs-lookup"><span data-stu-id="72750-1935">[To slash or not to slash](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)</span></span>
