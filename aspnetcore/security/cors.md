---
<span data-ttu-id="34751-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="34751-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="34751-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="34751-102">'Blazor'</span></span>
- <span data-ttu-id="34751-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="34751-103">'Identity'</span></span>
- <span data-ttu-id="34751-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="34751-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="34751-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="34751-105">'Razor'</span></span>
- <span data-ttu-id="34751-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="34751-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="34751-107">Abilitare le richieste tra le origini (CORS) in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34751-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34751-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="34751-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="34751-109">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34751-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="34751-110">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="34751-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="34751-111">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="34751-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="34751-112">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="34751-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="34751-113">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="34751-114">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="34751-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="34751-115">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="34751-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="34751-116">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="34751-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="34751-117">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="34751-118">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="34751-119">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="34751-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="34751-120">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="34751-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="34751-121">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="34751-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="34751-122">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34751-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="34751-123">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="34751-123">Same origin</span></span>

<span data-ttu-id="34751-124">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="34751-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="34751-125">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="34751-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="34751-126">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="34751-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="34751-127">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="34751-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="34751-128">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="34751-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="34751-129">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="34751-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="34751-130">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="34751-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="34751-131">Abilitare CORS</span><span class="sxs-lookup"><span data-stu-id="34751-131">Enable CORS</span></span>

<span data-ttu-id="34751-132">Sono disponibili tre modi per abilitare CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="34751-133">Nel middleware usando un criterio [denominato](#np) o un [criterio predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="34751-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="34751-134">Uso del [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34751-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="34751-135">Con l'attributo [[EnableCors]](#attr) .</span><span class="sxs-lookup"><span data-stu-id="34751-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="34751-136">L'utilizzo dell'attributo [[EnableCors]](#attr) con un criterio denominato fornisce il controllo più raffinato per limitare gli endpoint che supportano CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="34751-137">Ogni approccio è descritto in dettaglio nelle sezioni seguenti.</span><span class="sxs-lookup"><span data-stu-id="34751-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="34751-138">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="34751-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="34751-139">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="34751-140">Il codice seguente applica un criterio CORS a tutti gli endpoint dell'app con le origini specificate:</span><span class="sxs-lookup"><span data-stu-id="34751-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="34751-141">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="34751-141">The preceding code:</span></span>

* <span data-ttu-id="34751-142">Imposta il nome dei criteri su `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="34751-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="34751-143">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="34751-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="34751-144">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione e specifica il `_myAllowSpecificOrigins` criterio CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="34751-145">`UseCors`aggiunge il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="34751-146">La chiamata a `UseCors` deve essere posizionata dopo `UseRouting` , ma prima di `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="34751-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="34751-147">Per ulteriori informazioni, vedere l' [ordine del middleware](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="34751-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="34751-148">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="34751-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="34751-149">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="34751-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="34751-150">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="34751-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="34751-151">Abilita il `_myAllowSpecificOrigins` criterio CORS per tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="34751-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="34751-152">Vedere [endpoint routing](#ecors) per applicare un criterio CORS a endpoint specifici.</span><span class="sxs-lookup"><span data-stu-id="34751-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="34751-153">Con il routing dell'endpoint è **necessario** configurare il middleware CORS per l'esecuzione tra le chiamate a `UseRouting` e `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="34751-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="34751-154">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="34751-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="34751-155">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="34751-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="34751-156">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="34751-157">I <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodi possono essere concatenati, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="34751-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="34751-158">Nota: l'URL specificato **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="34751-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="34751-159">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="34751-160">CORS con criteri e middleware predefiniti</span><span class="sxs-lookup"><span data-stu-id="34751-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="34751-161">Il codice evidenziato seguente Abilita i criteri predefiniti di CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="34751-162">Il codice precedente applica i criteri predefiniti di CORS a tutti gli endpoint del controller.</span><span class="sxs-lookup"><span data-stu-id="34751-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="34751-163">Abilitare cors con routing degli endpoint</span><span class="sxs-lookup"><span data-stu-id="34751-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="34751-164">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="34751-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="34751-165">Per ulteriori informazioni, vedere [questo problema di GitHub](https://github.com/dotnet/aspnetcore/issues/20709) e [testare cors con endpoint routing e [HttpOptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="34751-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="34751-166">Con il routing degli endpoint, è possibile abilitare CORS in base all'endpoint usando il <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set di metodi di estensione:</span><span class="sxs-lookup"><span data-stu-id="34751-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="34751-167">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="34751-167">In the preceding code:</span></span>

* <span data-ttu-id="34751-168">`app.UseCors`Abilita il middleware CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="34751-169">Poiché un criterio predefinito non è stato configurato, `app.UseCors()` da solo non Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="34751-170">Gli `/echo` endpoint del controller e consentono richieste tra le origini usando i criteri specificati.</span><span class="sxs-lookup"><span data-stu-id="34751-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="34751-171">Gli `/echo2` Razor endpoint delle pagine e non **not** consentono richieste tra le origini perché non è stato specificato alcun criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="34751-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="34751-172">L'attributo [[DisableCors]](#dc) non **Disabilita CORS** che è stato abilitato dal routing di endpoint con `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="34751-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="34751-173">Per istruzioni sul test del codice simile a quello precedente, vedere [test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="34751-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="34751-174">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="34751-174">Enable CORS with attributes</span></span>

<span data-ttu-id="34751-175">L'abilitazione di CORS con l'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) e l'applicazione di un criterio denominato solo agli endpoint che richiedono CORS fornisce il controllo migliore.</span><span class="sxs-lookup"><span data-stu-id="34751-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="34751-176">L'attributo [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) rappresenta un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="34751-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="34751-177">L' `[EnableCors]` attributo Abilita CORS per gli endpoint selezionati, anziché tutti gli endpoint:</span><span class="sxs-lookup"><span data-stu-id="34751-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="34751-178">`[EnableCors]`Specifica i criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="34751-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="34751-179">`[EnableCors("{Policy String}")]`Specifica un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="34751-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="34751-180">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="34751-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="34751-181">Pagina`PageModel`</span><span class="sxs-lookup"><span data-stu-id="34751-181"> Page `PageModel`</span></span>
* <span data-ttu-id="34751-182">Controller</span><span class="sxs-lookup"><span data-stu-id="34751-182">Controller</span></span>
* <span data-ttu-id="34751-183">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="34751-183">Controller action method</span></span>

<span data-ttu-id="34751-184">È possibile applicare criteri diversi ai controller, ai modelli di pagina o ai metodi di azione con l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="34751-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="34751-185">Quando l' `[EnableCors]` attributo viene applicato a un controller, a un modello di pagina o a un metodo di azione e CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="34751-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="34751-186">**È consigliabile evitare di combinare i criteri. Usare l'** `[EnableCors]` **attributo o il middleware, non entrambi nella stessa app.**</span><span class="sxs-lookup"><span data-stu-id="34751-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="34751-187">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="34751-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="34751-188">Il codice seguente crea due criteri CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="34751-189">Per il controllo più raffinato della limitazione delle richieste CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="34751-190">Usare `[EnableCors("MyPolicy")]` con un criterio denominato.</span><span class="sxs-lookup"><span data-stu-id="34751-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="34751-191">Non definire un criterio predefinito.</span><span class="sxs-lookup"><span data-stu-id="34751-191">Don't define a default policy.</span></span>
* <span data-ttu-id="34751-192">Non usare il [routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34751-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="34751-193">Il codice nella sezione successiva soddisfa l'elenco precedente.</span><span class="sxs-lookup"><span data-stu-id="34751-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="34751-194">Vedere [test CORS](#testc) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="34751-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="34751-195">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="34751-195">Disable CORS</span></span>

<span data-ttu-id="34751-196">L'attributo [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) non **Disabilita CORS** che è stato abilitato dal routing dell' [endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34751-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="34751-197">Il codice seguente definisce i criteri CORS `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="34751-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="34751-198">Il codice seguente Disabilita CORS per l' `GetValues2` azione:</span><span class="sxs-lookup"><span data-stu-id="34751-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="34751-199">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="34751-199">The preceding code:</span></span>

* <span data-ttu-id="34751-200">Non Abilita CORS con [routing endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34751-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="34751-201">Non definisce un [criterio CORS predefinito](#dp).</span><span class="sxs-lookup"><span data-stu-id="34751-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="34751-202">USA [[EnableCors ("criteri")]](#attr) per abilitare il `"MyPolicy"` criterio CORS per il controller.</span><span class="sxs-lookup"><span data-stu-id="34751-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="34751-203">Disabilita CORS per il `GetValues2` metodo.</span><span class="sxs-lookup"><span data-stu-id="34751-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="34751-204">Vedere [test CORS](#testc) per istruzioni sul test del codice precedente.</span><span class="sxs-lookup"><span data-stu-id="34751-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="34751-205">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="34751-205">CORS policy options</span></span>

<span data-ttu-id="34751-206">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="34751-207">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="34751-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="34751-208">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="34751-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="34751-209">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="34751-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="34751-210">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="34751-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="34751-211">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="34751-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="34751-212">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="34751-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="34751-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="34751-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34751-214">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="34751-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="34751-215">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="34751-215">Set the allowed origins</span></span>

<span data-ttu-id="34751-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="34751-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="34751-217">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="34751-218">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="34751-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="34751-219">Il servizio CORS restituisce una risposta CORS non valida quando un'app è configurata con entrambi i metodi.</span><span class="sxs-lookup"><span data-stu-id="34751-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="34751-220">`AllowAnyOrigin`influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="34751-221">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34751-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="34751-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="34751-223">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="34751-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="34751-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="34751-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="34751-225">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="34751-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="34751-226">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="34751-227">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="34751-228">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="34751-228">Set the allowed request headers</span></span>

<span data-ttu-id="34751-229">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate [intestazioni richiesta autore](https://xhr.spec.whatwg.org/#request), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="34751-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="34751-230">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34751-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="34751-231">`AllowAnyHeader`influiscono sulle richieste preliminari e sull'intestazione [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) .</span><span class="sxs-lookup"><span data-stu-id="34751-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="34751-232">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34751-233">Un criterio middleware CORS corrisponde a intestazioni specifiche specificate da `WithHeaders` è possibile solo quando le intestazioni inviate `Access-Control-Request-Headers` corrispondono esattamente alle intestazioni indicate in `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="34751-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="34751-234">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="34751-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="34751-235">Il middleware CORS rifiuta una richiesta preliminare con la seguente intestazione di richiesta perché `Content-Language` ([HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) non è elencato in `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="34751-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="34751-236">L'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="34751-237">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="34751-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="34751-238">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="34751-238">Set the exposed response headers</span></span>

<span data-ttu-id="34751-239">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="34751-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="34751-240">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="34751-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="34751-241">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="34751-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="34751-242">La specifica CORS chiama queste intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="34751-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="34751-243">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34751-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="34751-244">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="34751-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="34751-245">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="34751-246">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="34751-247">Le credenziali includono i cookie e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="34751-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="34751-248">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="34751-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="34751-249">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="34751-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="34751-250">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="34751-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="34751-251">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="34751-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="34751-252">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="34751-252">The server must allow the credentials.</span></span> <span data-ttu-id="34751-253">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="34751-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="34751-254">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="34751-255">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34751-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="34751-256">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="34751-257">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="34751-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="34751-258">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="34751-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="34751-259">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="34751-259">Preflight requests</span></span>

<span data-ttu-id="34751-260">Per alcune richieste CORS, il browser invia una richiesta di [Opzioni](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) aggiuntive prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="34751-261">Questa richiesta è detta [richiesta preliminare](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span><span class="sxs-lookup"><span data-stu-id="34751-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="34751-262">Il browser può ignorare la richiesta preliminare se si verificano **tutte** le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="34751-263">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="34751-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="34751-264">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="34751-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="34751-265">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="34751-266">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="34751-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="34751-267">La specifica CORS chiama queste intestazioni di [richiesta autore](https://www.w3.org/TR/cors/#author-request-headers)intestazioni.</span><span class="sxs-lookup"><span data-stu-id="34751-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="34751-268">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="34751-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="34751-269">Di seguito è riportato un esempio di risposta simile alla richiesta preliminare eseguita dal pulsante **[Put test]** nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="34751-270">La richiesta preliminare usa il metodo delle [Opzioni http](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) .</span><span class="sxs-lookup"><span data-stu-id="34751-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="34751-271">Può includere le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-271">It may include the following headers:</span></span>

* <span data-ttu-id="34751-272">[Access-Control-request-method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): il metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="34751-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="34751-274">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="34751-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="34751-275">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="34751-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="34751-276">Se la richiesta preliminare viene negata, l'app restituisce una `200 OK` risposta ma non imposta le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="34751-277">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="34751-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="34751-278">Per un esempio di richiesta preliminare negata, vedere la sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="34751-279">Usando gli strumenti F12, l'app console Visualizza un errore simile a uno dei seguenti, a seconda del browser:</span><span class="sxs-lookup"><span data-stu-id="34751-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="34751-280">Firefox: richiesta tra le origini bloccata: gli stessi criteri di origine non consentono la lettura della risorsa remota in `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="34751-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="34751-281">(Motivo: richiesta CORS non riuscita).</span><span class="sxs-lookup"><span data-stu-id="34751-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="34751-282">Altre informazioni</span><span class="sxs-lookup"><span data-stu-id="34751-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="34751-283">Basata su cromo: l'accesso al recupero in ' https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 ' dall'origine ' https://cors3.azurewebsites.net ' è stato bloccato dal criterio CORS: la risposta alla richiesta preliminare non supera il controllo di accesso: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="34751-284">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="34751-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="34751-285">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34751-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="34751-286">Per consentire tutte le [intestazioni di richiesta di autore](https://www.w3.org/TR/cors/#author-request-headers), chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34751-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="34751-287">I browser non sono coerenti con la modalità di impostazione `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="34751-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="34751-288">Se uno:</span><span class="sxs-lookup"><span data-stu-id="34751-288">If either:</span></span>

* <span data-ttu-id="34751-289">Le intestazioni sono impostate su un valore diverso da`"*"`</span><span class="sxs-lookup"><span data-stu-id="34751-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="34751-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>viene chiamato: includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="34751-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="34751-291">Codice richiesta preliminare automatica</span><span class="sxs-lookup"><span data-stu-id="34751-291">Automatic preflight request code</span></span>

<span data-ttu-id="34751-292">Quando viene applicato il criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="34751-293">A livello globale, chiamando il `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="34751-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="34751-294">Utilizzando l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="34751-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="34751-295">ASP.NET Core risponde alla richiesta di opzioni preliminari.</span><span class="sxs-lookup"><span data-stu-id="34751-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="34751-296">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta le richieste preliminari automatiche.</span><span class="sxs-lookup"><span data-stu-id="34751-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="34751-297">Questo comportamento viene illustrato nella sezione [test CORS](#testc) di questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="34751-298">Attributo [HttpOptions] per le richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="34751-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="34751-299">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core risponde in genere alle richieste preliminari CORS automaticamente.</span><span class="sxs-lookup"><span data-stu-id="34751-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="34751-300">In alcuni scenari questo potrebbe non essere il caso.</span><span class="sxs-lookup"><span data-stu-id="34751-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="34751-301">Ad esempio, usando [cors con il routing degli endpoint](#ecors).</span><span class="sxs-lookup"><span data-stu-id="34751-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="34751-302">Il codice seguente usa l'attributo [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) per creare endpoint per le richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="34751-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="34751-303">Per istruzioni sul test del codice precedente [, vedere test CORS with endpoint routing e [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="34751-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="34751-304">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="34751-304">Set the preflight expiration time</span></span>

<span data-ttu-id="34751-305">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="34751-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="34751-306">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="34751-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="34751-307">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="34751-307">How CORS works</span></span>

<span data-ttu-id="34751-308">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="34751-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="34751-309">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="34751-310">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="34751-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="34751-311">Ad esempio, un attore malintenzionato potrebbe usare [lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="34751-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="34751-312">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="34751-313">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="34751-314">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="34751-315">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="34751-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="34751-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="34751-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="34751-317">Postman</span><span class="sxs-lookup"><span data-stu-id="34751-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="34751-318">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="34751-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="34751-319">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="34751-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="34751-320">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="34751-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="34751-321">I browser senza CORS non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="34751-322">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="34751-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="34751-323">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="34751-324">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="34751-325">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="34751-326">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="34751-327">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="34751-328">[Pulsante Put test](https://cors3.azurewebsites.net/test) nell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) distribuito</span><span class="sxs-lookup"><span data-stu-id="34751-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="34751-329">Di seguito è riportato un esempio di richiesta tra più origini dal pulsante test [valori](https://cors3.azurewebsites.net/) a `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="34751-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="34751-330">`Origin`Intestazione:</span><span class="sxs-lookup"><span data-stu-id="34751-330">The `Origin` header:</span></span>

* <span data-ttu-id="34751-331">Fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="34751-332">È obbligatorio e deve essere diverso dall'host.</span><span class="sxs-lookup"><span data-stu-id="34751-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="34751-333">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="34751-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="34751-334">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="34751-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="34751-335">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="34751-335">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="34751-336">Nelle `OPTIONS` richieste, il server imposta l'intestazione delle **intestazioni di risposta** `Access-Control-Allow-Origin: {allowed origin}` nella risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="34751-337">Ad esempio, la richiesta del pulsante [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) di [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito `OPTIONS` contiene le intestazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="34751-338">**Intestazioni generali**</span><span class="sxs-lookup"><span data-stu-id="34751-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="34751-339">**Intestazioni della risposta**</span><span class="sxs-lookup"><span data-stu-id="34751-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="34751-340">**Intestazioni della richiesta**</span><span class="sxs-lookup"><span data-stu-id="34751-340">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="34751-341">Nelle intestazioni di **risposta**precedenti, il server imposta l'intestazione [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) nella risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="34751-342">Il `https://cors1.azurewebsites.net` valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="34751-343">Se <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> viene chiamato, `Access-Control-Allow-Origin: *` viene restituito il valore del carattere jolly.</span><span class="sxs-lookup"><span data-stu-id="34751-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="34751-344">`AllowAnyOrigin`consente qualsiasi origine.</span><span class="sxs-lookup"><span data-stu-id="34751-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="34751-345">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34751-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="34751-346">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="34751-347">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="34751-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="34751-348">Richieste di opzioni di visualizzazione</span><span class="sxs-lookup"><span data-stu-id="34751-348">Display OPTIONS requests</span></span>

<span data-ttu-id="34751-349">Per impostazione predefinita, i browser Chrome e Edge non visualizzano le richieste OPTIONS nella scheda rete degli strumenti F12.</span><span class="sxs-lookup"><span data-stu-id="34751-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="34751-350">Per visualizzare le richieste di opzioni in questi browser:</span><span class="sxs-lookup"><span data-stu-id="34751-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="34751-351">`chrome://flags/#out-of-blink-cors` o `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="34751-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="34751-352">disabilitare il flag.</span><span class="sxs-lookup"><span data-stu-id="34751-352">disable the flag.</span></span>
* <span data-ttu-id="34751-353">Riavvia.</span><span class="sxs-lookup"><span data-stu-id="34751-353">restart.</span></span>

<span data-ttu-id="34751-354">Per impostazione predefinita, Firefox Visualizza le richieste OPTIONS.</span><span class="sxs-lookup"><span data-stu-id="34751-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="34751-355">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="34751-355">CORS in IIS</span></span>

<span data-ttu-id="34751-356">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="34751-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="34751-357">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="34751-358">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="34751-358">Test CORS</span></span>

<span data-ttu-id="34751-359">Il [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) contiene codice per testare CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="34751-360">Vedere [come scaricare un esempio](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="34751-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="34751-361">L'esempio è un progetto API con Razor pagine aggiunte:</span><span class="sxs-lookup"><span data-stu-id="34751-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="34751-362">`WithOrigins("https://localhost:<port>");`deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="34751-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="34751-363">Di seguito sono riportati `ValuesController` gli endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="34751-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="34751-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) viene fornito dal pacchetto NuGet [Rick. docs. Samples. RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) e visualizza le informazioni sulla route.</span><span class="sxs-lookup"><span data-stu-id="34751-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="34751-365">Testare il codice di esempio precedente usando uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="34751-366">Usare l'app di esempio distribuita all'indirizzo [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="34751-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="34751-367">Non è necessario scaricare l'esempio.</span><span class="sxs-lookup"><span data-stu-id="34751-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="34751-368">Eseguire l'esempio con `dotnet run` utilizzando l'URL predefinito `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="34751-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="34751-369">Eseguire l'esempio da Visual Studio con la porta impostata su 44398 per un URL di `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="34751-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="34751-370">Uso di un browser con gli strumenti F12:</span><span class="sxs-lookup"><span data-stu-id="34751-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="34751-371">Selezionare il pulsante **valori** ed esaminare le intestazioni nella scheda **rete** .</span><span class="sxs-lookup"><span data-stu-id="34751-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="34751-372">Selezionare il pulsante **put test (Put test** ).</span><span class="sxs-lookup"><span data-stu-id="34751-372">Select the **PUT test** button.</span></span> <span data-ttu-id="34751-373">Per istruzioni sulla visualizzazione della richiesta OPTIONS, vedere [visualizzare le richieste di opzioni](#options) .</span><span class="sxs-lookup"><span data-stu-id="34751-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="34751-374">Il **test put** crea due richieste, una richiesta preliminare Options e la richiesta PUT.</span><span class="sxs-lookup"><span data-stu-id="34751-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="34751-375">Selezionare il **`GetValues2 [DisableCors]`** pulsante per attivare una richiesta CORS non riuscita.</span><span class="sxs-lookup"><span data-stu-id="34751-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="34751-376">Come indicato nel documento, la risposta restituisce 200 esito positivo, ma la richiesta CORS non viene eseguita.</span><span class="sxs-lookup"><span data-stu-id="34751-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="34751-377">Selezionare la scheda **console** per visualizzare l'errore CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="34751-378">A seconda del browser, viene visualizzato un errore simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="34751-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="34751-379">L'accesso al recupero `'https://cors1.azurewebsites.net/api/values/GetValues2'` dall'origine `'https://cors3.azurewebsites.net'` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="34751-380">Se si ritiene soddisfacente una risposta opaca, impostare la modalità della richiesta 'no-cors' per recuperare la risorsa con CORS disabilitato.</span><span class="sxs-lookup"><span data-stu-id="34751-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="34751-381">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="34751-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="34751-382">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="34751-383">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="34751-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="34751-384">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="34751-385">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="34751-386">Il comando seguente usa `curl` per emettere una richiesta OPTIONS con informazioni:</span><span class="sxs-lookup"><span data-stu-id="34751-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="34751-387">Test di CORS con routing endpoint e [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="34751-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="34751-388">L'abilitazione di CORS in base all'endpoint `RequireCors` attualmente in **uso non** supporta [le richieste preliminari automatiche](#apf).</span><span class="sxs-lookup"><span data-stu-id="34751-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="34751-389">Si consideri il codice seguente che usa il [routing dell'endpoint per abilitare CORS](#ecors):</span><span class="sxs-lookup"><span data-stu-id="34751-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="34751-390">Di seguito sono riportati gli `TodoItems1Controller` endpoint per i test:</span><span class="sxs-lookup"><span data-stu-id="34751-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="34751-391">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=1) dell' [esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)distribuito.</span><span class="sxs-lookup"><span data-stu-id="34751-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="34751-392">I pulsanti **Delete [EnableCors]** e **Get [EnableCors]** sono riusciti perché gli endpoint hanno `[EnableCors]` e rispondono alle richieste preliminari.</span><span class="sxs-lookup"><span data-stu-id="34751-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="34751-393">Gli altri endpoint hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34751-393">The other endpoints fails.</span></span> <span data-ttu-id="34751-394">Il pulsante **Get** ha esito negativo perché il [codice JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Invia:</span><span class="sxs-lookup"><span data-stu-id="34751-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="34751-395">Di seguito `TodoItems2Controller` vengono forniti endpoint simili, ma è incluso codice esplicito per rispondere alle richieste di opzioni:</span><span class="sxs-lookup"><span data-stu-id="34751-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="34751-396">Testare il codice precedente dalla [pagina di test](https://cors1.azurewebsites.net/test?number=2) dell'esempio distribuito.</span><span class="sxs-lookup"><span data-stu-id="34751-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="34751-397">Nell'elenco a discesa **controller** selezionare **preliminare** e quindi **impostare controller**.</span><span class="sxs-lookup"><span data-stu-id="34751-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="34751-398">Tutte le chiamate CORS agli `TodoItems2Controller` endpoint hanno esito positivo.</span><span class="sxs-lookup"><span data-stu-id="34751-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34751-399">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="34751-399">Additional resources</span></span>

* [<span data-ttu-id="34751-400">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="34751-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="34751-401">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="34751-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34751-402">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="34751-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="34751-403">Questo articolo illustra come abilitare CORS in un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34751-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="34751-404">La sicurezza del browser impedisce a una pagina Web di eseguire richieste a un dominio diverso da quello che ha gestito la pagina Web.</span><span class="sxs-lookup"><span data-stu-id="34751-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="34751-405">Questa restrizione è detta *criterio della stessa origine*.</span><span class="sxs-lookup"><span data-stu-id="34751-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="34751-406">Il criterio della stessa origine impedisce a un sito dannoso di leggere dati sensibili da un altro sito.</span><span class="sxs-lookup"><span data-stu-id="34751-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="34751-407">In alcuni casi potrebbe essere necessario consentire ad altri siti di effettuare richieste tra le origini all'app.</span><span class="sxs-lookup"><span data-stu-id="34751-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="34751-408">Per altre informazioni, vedere l' [articolo relativo a Mozilla CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="34751-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="34751-409">[Condivisione risorse tra le origini](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="34751-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="34751-410">È uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="34751-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="34751-411">**Non** è una funzionalità di sicurezza, CORS rilassa la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="34751-412">Un'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="34751-413">Per ulteriori informazioni, vedere [funzionamento di CORS](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="34751-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="34751-414">Consente a un server di consentire in modo esplicito alcune richieste tra origini rifiutando altre.</span><span class="sxs-lookup"><span data-stu-id="34751-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="34751-415">È più sicuro e flessibile rispetto alle tecniche precedenti, ad esempio [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="34751-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="34751-416">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34751-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="34751-417">Stessa origine</span><span class="sxs-lookup"><span data-stu-id="34751-417">Same origin</span></span>

<span data-ttu-id="34751-418">Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="34751-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="34751-419">Questi due URL hanno la stessa origine:</span><span class="sxs-lookup"><span data-stu-id="34751-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="34751-420">Questi URL hanno origini diverse da quelle dei due URL precedenti:</span><span class="sxs-lookup"><span data-stu-id="34751-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="34751-421">`https://example.net`: Dominio diverso</span><span class="sxs-lookup"><span data-stu-id="34751-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="34751-422">`https://www.example.com/foo.html`: Sottodominio diverso</span><span class="sxs-lookup"><span data-stu-id="34751-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="34751-423">`http://example.com/foo.html`: Schema diverso</span><span class="sxs-lookup"><span data-stu-id="34751-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="34751-424">`https://example.com:9000/foo.html`: Porta diversa</span><span class="sxs-lookup"><span data-stu-id="34751-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="34751-425">Internet Explorer non considera la porta durante il confronto delle origini.</span><span class="sxs-lookup"><span data-stu-id="34751-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="34751-426">CORS con criteri e middleware denominati</span><span class="sxs-lookup"><span data-stu-id="34751-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="34751-427">Il middleware CORS gestisce le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="34751-428">Il codice seguente Abilita CORS per l'intera app con l'origine specificata:</span><span class="sxs-lookup"><span data-stu-id="34751-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="34751-429">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="34751-429">The preceding code:</span></span>

* <span data-ttu-id="34751-430">Imposta il nome del criterio su " \_ myAllowSpecificOrigins".</span><span class="sxs-lookup"><span data-stu-id="34751-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="34751-431">Il nome del criterio è arbitrario.</span><span class="sxs-lookup"><span data-stu-id="34751-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="34751-432">Chiama il <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> metodo di estensione, che Abilita CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="34751-433">Chiama <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> con un' [espressione lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="34751-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="34751-434">L'espressione lambda accetta un <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> oggetto.</span><span class="sxs-lookup"><span data-stu-id="34751-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="34751-435">Le [Opzioni di configurazione](#cors-policy-options), ad esempio `WithOrigins` , sono descritte più avanti in questo articolo.</span><span class="sxs-lookup"><span data-stu-id="34751-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="34751-436">La <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> chiamata al metodo aggiunge i servizi CORS al contenitore del servizio dell'app:</span><span class="sxs-lookup"><span data-stu-id="34751-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="34751-437">Per ulteriori informazioni, vedere [Opzioni dei criteri CORS](#cpo) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="34751-438">Il <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> metodo può concatenare i metodi, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="34751-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="34751-439">Nota: l'URL **non** deve contenere una barra finale ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="34751-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="34751-440">Se l'URL termina con `/` , il confronto restituisce `false` e non viene restituita alcuna intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="34751-441">Il codice seguente applica i criteri di CORS a tutti gli endpoint di app tramite il middleware CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="34751-442">Nota: `UseCors` è necessario chiamare prima `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="34751-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="34751-443">Vedere [abilitare CORS in Razor pagine, controller e metodi di azione](#ecors) per applicare i criteri di CORS a livello di pagina/controller/azione.</span><span class="sxs-lookup"><span data-stu-id="34751-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="34751-444">Vedere [test CORS](#test) per istruzioni sul test di codice simile al codice precedente.</span><span class="sxs-lookup"><span data-stu-id="34751-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="34751-445">Abilita CORS con attributi</span><span class="sxs-lookup"><span data-stu-id="34751-445">Enable CORS with attributes</span></span>

<span data-ttu-id="34751-446">L'attributo [ &lbrack; EnableCors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) fornisce un'alternativa all'applicazione di CORS a livello globale.</span><span class="sxs-lookup"><span data-stu-id="34751-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="34751-447">L' `[EnableCors]` attributo Abilita CORS per i punti finali selezionati, anziché tutti i punti finali.</span><span class="sxs-lookup"><span data-stu-id="34751-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="34751-448">Usare `[EnableCors]` per specificare i criteri predefiniti e `[EnableCors("{Policy String}")]` per specificare un criterio.</span><span class="sxs-lookup"><span data-stu-id="34751-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="34751-449">L' `[EnableCors]` attributo può essere applicato a:</span><span class="sxs-lookup"><span data-stu-id="34751-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="34751-450">Pagina`PageModel`</span><span class="sxs-lookup"><span data-stu-id="34751-450"> Page `PageModel`</span></span>
* <span data-ttu-id="34751-451">Controller</span><span class="sxs-lookup"><span data-stu-id="34751-451">Controller</span></span>
* <span data-ttu-id="34751-452">Metodo di azione del controller</span><span class="sxs-lookup"><span data-stu-id="34751-452">Controller action method</span></span>

<span data-ttu-id="34751-453">È possibile applicare criteri diversi a controller/pagina-modello/azione con l' `[EnableCors]` attributo.</span><span class="sxs-lookup"><span data-stu-id="34751-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="34751-454">Quando l' `[EnableCors]` attributo viene applicato a un metodo di azione/modello di pagina, mentre CORS è abilitato nel middleware, vengono applicati **entrambi** i criteri.</span><span class="sxs-lookup"><span data-stu-id="34751-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="34751-455">Si consiglia di **non** combinare i criteri.</span><span class="sxs-lookup"><span data-stu-id="34751-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="34751-456">Usare l' `[EnableCors]` attributo o il middleware, **non entrambi**.</span><span class="sxs-lookup"><span data-stu-id="34751-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="34751-457">Quando `[EnableCors]` si usa, **non** definire criteri predefiniti.</span><span class="sxs-lookup"><span data-stu-id="34751-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="34751-458">Il codice seguente applica un criterio diverso a ogni metodo:</span><span class="sxs-lookup"><span data-stu-id="34751-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="34751-459">Il codice seguente crea un criterio predefinito CORS e un criterio denominato `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="34751-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="34751-460">Disabilitare CORS</span><span class="sxs-lookup"><span data-stu-id="34751-460">Disable CORS</span></span>

<span data-ttu-id="34751-461">L'attributo [ &lbrack; DISABLECORS &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Disabilita CORS per il controller, il modello di pagina o l'azione.</span><span class="sxs-lookup"><span data-stu-id="34751-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="34751-462">Opzioni dei criteri di CORS</span><span class="sxs-lookup"><span data-stu-id="34751-462">CORS policy options</span></span>

<span data-ttu-id="34751-463">In questa sezione vengono descritte le varie opzioni che è possibile impostare in un criterio CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="34751-464">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="34751-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="34751-465">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="34751-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="34751-466">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="34751-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="34751-467">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="34751-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="34751-468">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="34751-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="34751-469">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="34751-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="34751-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>viene chiamato in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="34751-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="34751-471">Per alcune opzioni, può essere utile leggere prima la sezione [come funziona CORS](#how-cors) .</span><span class="sxs-lookup"><span data-stu-id="34751-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="34751-472">Imposta le origini consentite</span><span class="sxs-lookup"><span data-stu-id="34751-472">Set the allowed origins</span></span>

<span data-ttu-id="34751-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Consente le richieste CORS da tutte le origini con qualsiasi schema ( `http` o `https` ).</span><span class="sxs-lookup"><span data-stu-id="34751-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="34751-474">`AllowAnyOrigin`non è sicuro perché *qualsiasi sito Web* può effettuare richieste tra origini per l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="34751-475">La definizione di `AllowAnyOrigin` e `AllowCredentials` è una configurazione non sicura e può comportare la falsificazione della richiesta tra siti.</span><span class="sxs-lookup"><span data-stu-id="34751-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="34751-476">Per un'app sicura, specificare un elenco esatto di origini se il client deve autorizzare se stesso ad accedere alle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="34751-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="34751-477">`AllowAnyOrigin`influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Origin` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="34751-478">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34751-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Imposta la <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> proprietà dei criteri in modo che sia una funzione che consente alle origini di corrispondere a un dominio con caratteri jolly configurato durante la valutazione se l'origine è consentita.</span><span class="sxs-lookup"><span data-stu-id="34751-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="34751-480">Impostare i metodi HTTP consentiti</span><span class="sxs-lookup"><span data-stu-id="34751-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="34751-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="34751-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="34751-482">Consente qualsiasi metodo HTTP:</span><span class="sxs-lookup"><span data-stu-id="34751-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="34751-483">Influiscono sulle richieste preliminari e sull' `Access-Control-Allow-Methods` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="34751-484">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="34751-485">Impostare le intestazioni della richiesta consentita</span><span class="sxs-lookup"><span data-stu-id="34751-485">Set the allowed request headers</span></span>

<span data-ttu-id="34751-486">Per consentire l'invio di intestazioni specifiche in una richiesta CORS, denominate *intestazioni richiesta autore*, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> e specificare le intestazioni consentite:</span><span class="sxs-lookup"><span data-stu-id="34751-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="34751-487">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34751-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="34751-488">Questa impostazione influiscono sulle richieste preliminari e sull' `Access-Control-Request-Headers` intestazione.</span><span class="sxs-lookup"><span data-stu-id="34751-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="34751-489">Per ulteriori informazioni, vedere la sezione [preflight requests](#preflight-requests) .</span><span class="sxs-lookup"><span data-stu-id="34751-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="34751-490">Il middleware CORS consente sempre l'invio di quattro intestazioni in, `Access-Control-Request-Headers` indipendentemente dai valori configurati in CorsPolicy. Headers.</span><span class="sxs-lookup"><span data-stu-id="34751-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="34751-491">Questo elenco di intestazioni include:</span><span class="sxs-lookup"><span data-stu-id="34751-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="34751-492">Si consideri, ad esempio, un'app configurata come segue:</span><span class="sxs-lookup"><span data-stu-id="34751-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="34751-493">Il middleware CORS risponde correttamente a una richiesta preliminare con la seguente intestazione di richiesta perché è sempre consentita `Content-Language` :</span><span class="sxs-lookup"><span data-stu-id="34751-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="34751-494">Impostare le intestazioni di risposta esposte</span><span class="sxs-lookup"><span data-stu-id="34751-494">Set the exposed response headers</span></span>

<span data-ttu-id="34751-495">Per impostazione predefinita, il browser non espone tutte le intestazioni di risposta all'app.</span><span class="sxs-lookup"><span data-stu-id="34751-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="34751-496">Per altre informazioni, vedere la pagina relativa alla [condivisione delle risorse tra le origini W3C (terminologia): intestazione della risposta semplice](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="34751-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="34751-497">Le intestazioni di risposta disponibili per impostazione predefinita sono:</span><span class="sxs-lookup"><span data-stu-id="34751-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="34751-498">La specifica CORS chiama queste intestazioni di *risposta semplici*.</span><span class="sxs-lookup"><span data-stu-id="34751-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="34751-499">Per rendere disponibili altre intestazioni per l'app, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34751-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="34751-500">Credenziali nelle richieste tra le origini</span><span class="sxs-lookup"><span data-stu-id="34751-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="34751-501">Le credenziali richiedono una gestione speciale in una richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="34751-502">Per impostazione predefinita, il browser non invia le credenziali con una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="34751-503">Le credenziali includono i cookie e gli schemi di autenticazione HTTP.</span><span class="sxs-lookup"><span data-stu-id="34751-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="34751-504">Per inviare le credenziali con una richiesta tra le origini, il client deve impostare `XMLHttpRequest.withCredentials` su `true` .</span><span class="sxs-lookup"><span data-stu-id="34751-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="34751-505">Utilizzando `XMLHttpRequest` direttamente:</span><span class="sxs-lookup"><span data-stu-id="34751-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="34751-506">Uso di jQuery:</span><span class="sxs-lookup"><span data-stu-id="34751-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="34751-507">Uso dell' [API fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="34751-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="34751-508">Il server deve consentire le credenziali.</span><span class="sxs-lookup"><span data-stu-id="34751-508">The server must allow the credentials.</span></span> <span data-ttu-id="34751-509">Per consentire le credenziali tra le origini, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="34751-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="34751-510">La risposta HTTP include un' `Access-Control-Allow-Credentials` intestazione che indica al browser che il server consente le credenziali per una richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="34751-511">Se il browser invia le credenziali, ma la risposta non include un' `Access-Control-Allow-Credentials` intestazione valida, il browser non espone la risposta all'app e la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34751-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="34751-512">Consentire le credenziali tra le origini costituisce un rischio per la sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="34751-513">Un sito Web in un altro dominio può inviare le credenziali dell'utente che ha eseguito l'accesso all'app per conto dell'utente senza la conoscenza dell'utente.</span><span class="sxs-lookup"><span data-stu-id="34751-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="34751-514">La specifica CORS indica inoltre che l'impostazione delle origini su `"*"` (tutte le origini) non è valida se l' `Access-Control-Allow-Credentials` intestazione è presente.</span><span class="sxs-lookup"><span data-stu-id="34751-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="34751-515">Richieste preliminari</span><span class="sxs-lookup"><span data-stu-id="34751-515">Preflight requests</span></span>

<span data-ttu-id="34751-516">Per alcune richieste CORS, il browser invia una richiesta aggiuntiva prima di effettuare la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="34751-517">Questa richiesta è detta *richiesta preliminare*.</span><span class="sxs-lookup"><span data-stu-id="34751-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="34751-518">Il browser può ignorare la richiesta preliminare se vengono soddisfatte le condizioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="34751-519">Il metodo di richiesta è GET, HEAD o POST.</span><span class="sxs-lookup"><span data-stu-id="34751-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="34751-520">L'app non imposta intestazioni di richiesta diverse da `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` o `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="34751-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="34751-521">L' `Content-Type` intestazione, se impostata, presenta uno dei valori seguenti:</span><span class="sxs-lookup"><span data-stu-id="34751-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="34751-522">La regola sulle intestazioni di richiesta impostate per la richiesta del client si applica alle intestazioni impostate dall'app chiamando `setRequestHeader` sull' `XMLHttpRequest` oggetto.</span><span class="sxs-lookup"><span data-stu-id="34751-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="34751-523">La specifica CORS chiama queste intestazioni di *richiesta autore*intestazioni.</span><span class="sxs-lookup"><span data-stu-id="34751-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="34751-524">La regola non si applica alle intestazioni che il browser può impostare, ad esempio `User-Agent` , `Host` o `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="34751-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="34751-525">Di seguito è riportato un esempio di una richiesta preliminare:</span><span class="sxs-lookup"><span data-stu-id="34751-525">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="34751-526">La richiesta pre-flight usa il metodo delle opzioni HTTP.</span><span class="sxs-lookup"><span data-stu-id="34751-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="34751-527">Sono incluse due intestazioni speciali:</span><span class="sxs-lookup"><span data-stu-id="34751-527">It includes two special headers:</span></span>

* <span data-ttu-id="34751-528">`Access-Control-Request-Method`: Metodo HTTP che verrà usato per la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="34751-529">`Access-Control-Request-Headers`: Elenco di intestazioni di richiesta impostate dall'app sulla richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="34751-530">Come indicato in precedenza, non include le intestazioni impostate dal browser, ad esempio `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="34751-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="34751-531">Quando CORS è abilitato con i criteri appropriati, ASP.NET Core in genere risponde automaticamente alle richieste preliminari di CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="34751-532">Vedere l' [attributo [HttpOptions] per le richieste preliminari](#pro).</span><span class="sxs-lookup"><span data-stu-id="34751-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="34751-533">Una richiesta preliminare CORS può includere un' `Access-Control-Request-Headers` intestazione, che indica al server le intestazioni inviate con la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="34751-534">Per consentire intestazioni specifiche, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="34751-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="34751-535">Per consentire tutte le intestazioni di richiesta di autore, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="34751-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="34751-536">I browser non sono completamente coerenti nel modo in cui vengono impostati `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="34751-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="34751-537">Se si impostano le intestazioni su un valore diverso da `"*"` (o si utilizza <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), è necessario includere almeno `Accept` , `Content-Type` e `Origin` , più eventuali intestazioni personalizzate che si desidera supportare.</span><span class="sxs-lookup"><span data-stu-id="34751-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="34751-538">Di seguito è riportato un esempio di risposta alla richiesta preliminare (presupponendo che il server consenta la richiesta):</span><span class="sxs-lookup"><span data-stu-id="34751-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="34751-539">La risposta include un' `Access-Control-Allow-Methods` intestazione che elenca i metodi consentiti e, facoltativamente `Access-Control-Allow-Headers` , un'intestazione, che elenca le intestazioni consentite.</span><span class="sxs-lookup"><span data-stu-id="34751-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="34751-540">Se la richiesta preliminare ha esito positivo, il browser invia la richiesta effettiva.</span><span class="sxs-lookup"><span data-stu-id="34751-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="34751-541">Se la richiesta preliminare viene negata, l'app restituisce una risposta *200 OK* , ma non invia le intestazioni CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="34751-542">Pertanto, il browser non tenta di eseguire la richiesta tra origini.</span><span class="sxs-lookup"><span data-stu-id="34751-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="34751-543">Imposta la data di scadenza preliminare</span><span class="sxs-lookup"><span data-stu-id="34751-543">Set the preflight expiration time</span></span>

<span data-ttu-id="34751-544">L' `Access-Control-Max-Age` intestazione specifica per quanto tempo la risposta alla richiesta preliminare può essere memorizzata nella cache.</span><span class="sxs-lookup"><span data-stu-id="34751-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="34751-545">Per impostare questa intestazione, chiamare <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="34751-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="34751-546">Funzionamento di CORS</span><span class="sxs-lookup"><span data-stu-id="34751-546">How CORS works</span></span>

<span data-ttu-id="34751-547">In questa sezione viene descritto cosa accade in una richiesta [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) al livello dei messaggi HTTP.</span><span class="sxs-lookup"><span data-stu-id="34751-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="34751-548">CORS **non** è una funzionalità di sicurezza.</span><span class="sxs-lookup"><span data-stu-id="34751-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="34751-549">CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine.</span><span class="sxs-lookup"><span data-stu-id="34751-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="34751-550">Ad esempio, un attore malintenzionato potrebbe usare [Impedisci lo scripting tra siti (XSS)](xref:security/cross-site-scripting) per il sito ed eseguire una richiesta tra siti al sito abilitato per CORS per rubare le informazioni.</span><span class="sxs-lookup"><span data-stu-id="34751-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="34751-551">L'API non è più sicura consentendo CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="34751-552">Il client (browser) deve applicare CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="34751-553">Il server esegue la richiesta e restituisce la risposta, ovvero il client che restituisce un errore e blocca la risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="34751-554">Uno degli strumenti seguenti, ad esempio, visualizzerà la risposta del server:</span><span class="sxs-lookup"><span data-stu-id="34751-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="34751-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="34751-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="34751-556">Postman</span><span class="sxs-lookup"><span data-stu-id="34751-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="34751-557">HttpClient .NET</span><span class="sxs-lookup"><span data-stu-id="34751-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="34751-558">Un Web browser immettendo l'URL nella barra degli indirizzi.</span><span class="sxs-lookup"><span data-stu-id="34751-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="34751-559">Si tratta di un modo per consentire a un server di consentire ai browser di eseguire una richiesta dell' [API di recupero](https://developer.mozilla.org/docs/Web/API/Fetch_API) o di [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) tra le origini, che altrimenti sarebbe proibita.</span><span class="sxs-lookup"><span data-stu-id="34751-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="34751-560">I browser (senza CORS) non possono eseguire richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="34751-561">Prima di CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) è stato usato per aggirare questa restrizione.</span><span class="sxs-lookup"><span data-stu-id="34751-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="34751-562">JSONP non usa XHR, usa il `<script>` tag per ricevere la risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="34751-563">Gli script possono essere caricati tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="34751-564">La [specifica CORS](https://www.w3.org/TR/cors/) ha introdotto diverse nuove intestazioni HTTP che consentono richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="34751-565">Se un browser supporta CORS, queste intestazioni vengono impostate automaticamente per le richieste tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="34751-566">Il codice JavaScript personalizzato non è necessario per abilitare CORS.</span><span class="sxs-lookup"><span data-stu-id="34751-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="34751-567">Di seguito è riportato un esempio di richiesta tra le origini.</span><span class="sxs-lookup"><span data-stu-id="34751-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="34751-568">L' `Origin` intestazione fornisce il dominio del sito che sta effettuando la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="34751-569">L' `Origin` intestazione è obbligatoria e deve essere diversa dall'host.</span><span class="sxs-lookup"><span data-stu-id="34751-569">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="34751-570">Se il server consente la richiesta, imposta l' `Access-Control-Allow-Origin` intestazione nella risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="34751-571">Il valore di questa intestazione corrisponde all' `Origin` intestazione della richiesta o è il valore del carattere jolly `"*"` , ovvero qualsiasi origine è consentita:</span><span class="sxs-lookup"><span data-stu-id="34751-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="34751-572">Se la risposta non include l' `Access-Control-Allow-Origin` intestazione, la richiesta tra le origini ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="34751-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="34751-573">In particolare, il browser non consente la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="34751-574">Anche se il server restituisce una risposta con esito positivo, il browser non rende disponibile la risposta all'app client.</span><span class="sxs-lookup"><span data-stu-id="34751-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="34751-575">Testare CORS</span><span class="sxs-lookup"><span data-stu-id="34751-575">Test CORS</span></span>

<span data-ttu-id="34751-576">Per testare CORS:</span><span class="sxs-lookup"><span data-stu-id="34751-576">To test CORS:</span></span>

1. <span data-ttu-id="34751-577">[Creare un progetto API](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="34751-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="34751-578">In alternativa, è possibile [scaricare l'esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="34751-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="34751-579">Abilitare CORS usando uno degli approcci descritti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="34751-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="34751-580">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="34751-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="34751-581">`WithOrigins("https://localhost:<port>");`deve essere usato solo per il test di un'app di esempio simile al [codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)per il download.</span><span class="sxs-lookup"><span data-stu-id="34751-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="34751-582">Creare un progetto di app Web ( Razor pagine o MVC).</span><span class="sxs-lookup"><span data-stu-id="34751-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="34751-583">Nell'esempio vengono utilizzate le Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="34751-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="34751-584">È possibile creare l'app Web nella stessa soluzione del progetto API.</span><span class="sxs-lookup"><span data-stu-id="34751-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="34751-585">Aggiungere il codice evidenziato seguente al file *index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="34751-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="34751-586">Nel codice precedente sostituire `url: 'https://<web app>.azurewebsites.net/api/values/1',` con l'URL dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="34751-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="34751-587">Distribuire il progetto API.</span><span class="sxs-lookup"><span data-stu-id="34751-587">Deploy the API project.</span></span> <span data-ttu-id="34751-588">Ad esempio, [eseguire la distribuzione in Azure](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="34751-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="34751-589">Eseguire le Razor pagine o l'app MVC dal desktop e fare clic sul pulsante **test** .</span><span class="sxs-lookup"><span data-stu-id="34751-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="34751-590">Usare gli strumenti F12 per esaminare i messaggi di errore.</span><span class="sxs-lookup"><span data-stu-id="34751-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="34751-591">Rimuovere l'origine localhost da `WithOrigins` e distribuire l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="34751-592">In alternativa, eseguire l'app client con una porta diversa.</span><span class="sxs-lookup"><span data-stu-id="34751-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="34751-593">Ad esempio, eseguire da Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="34751-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="34751-594">Eseguire il test con l'app client.</span><span class="sxs-lookup"><span data-stu-id="34751-594">Test with the client app.</span></span> <span data-ttu-id="34751-595">Gli errori CORS restituiscono un errore, ma il messaggio di errore non è disponibile per JavaScript.</span><span class="sxs-lookup"><span data-stu-id="34751-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="34751-596">Usare la scheda console negli strumenti F12 per visualizzare l'errore.</span><span class="sxs-lookup"><span data-stu-id="34751-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="34751-597">A seconda del browser, viene ricevuto un errore (nella console strumenti F12) simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="34751-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="34751-598">Uso di Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="34751-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="34751-599">**SEC7120: [CORS] l'origine `https://localhost:44375` non è stata trovata `https://localhost:44375` nell'intestazione della risposta Access-Control-Allow-Origin per la risorsa tra le origini in`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="34751-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="34751-600">Uso di Chrome:</span><span class="sxs-lookup"><span data-stu-id="34751-600">Using Chrome:</span></span>

     <span data-ttu-id="34751-601">**L'accesso a XMLHttpRequest `https://webapi.azurewebsites.net/api/values/1` da Origin `https://localhost:44375` è stato bloccato dal criterio CORS: non è presente alcuna intestazione ' Access-Control-Allow-Origin ' nella risorsa richiesta.**</span><span class="sxs-lookup"><span data-stu-id="34751-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="34751-602">Gli endpoint abilitati per CORS possono essere testati con uno strumento, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [postazione](https://www.getpostman.com/).</span><span class="sxs-lookup"><span data-stu-id="34751-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="34751-603">Quando si utilizza uno strumento, l'origine della richiesta specificata dall' `Origin` intestazione deve essere diversa dall'host che riceve la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="34751-604">Se la richiesta non è *tra origini* basate sul valore dell' `Origin` intestazione:</span><span class="sxs-lookup"><span data-stu-id="34751-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="34751-605">Non è necessario che il middleware CORS elabori la richiesta.</span><span class="sxs-lookup"><span data-stu-id="34751-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="34751-606">Le intestazioni CORS non vengono restituite nella risposta.</span><span class="sxs-lookup"><span data-stu-id="34751-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="34751-607">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="34751-607">CORS in IIS</span></span>

<span data-ttu-id="34751-608">Quando si esegue la distribuzione in IIS, CORS deve essere eseguito prima dell'autenticazione di Windows se il server non è configurato per consentire l'accesso anonimo.</span><span class="sxs-lookup"><span data-stu-id="34751-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="34751-609">Per supportare questo scenario, è necessario installare e configurare il [modulo cors di IIS](https://www.iis.net/downloads/microsoft/iis-cors-module) per l'app.</span><span class="sxs-lookup"><span data-stu-id="34751-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="34751-610">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="34751-610">Additional resources</span></span>

* [<span data-ttu-id="34751-611">Condivisione risorse tra le origini (CORS)</span><span class="sxs-lookup"><span data-stu-id="34751-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="34751-612">Introduzione al modulo CORS di IIS</span><span class="sxs-lookup"><span data-stu-id="34751-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
