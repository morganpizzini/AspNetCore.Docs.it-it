---
<span data-ttu-id="b8bd2-101">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-102">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-103">'Identity'</span></span>
- <span data-ttu-id="b8bd2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-105">'Razor'</span></span>
- <span data-ttu-id="b8bd2-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="b8bd2-107">Formattare i dati di risposta nell'API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8bd2-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="b8bd2-108">Di [Rick Anderson](https://twitter.com/RickAndMSFT) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b8bd2-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b8bd2-109">ASP.NET Core MVC supporta la formattazione dei dati di risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="b8bd2-110">I dati di risposta possono essere formattati usando formati specifici o in risposta al formato richiesto dal client.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="b8bd2-111">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8bd2-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="b8bd2-112">Risultati azione specifici del formato</span><span class="sxs-lookup"><span data-stu-id="b8bd2-112">Format-specific Action Results</span></span>

<span data-ttu-id="b8bd2-113">Alcuni tipi di risultati di azioni sono specifici di un particolare formato, ad esempio <xref:Microsoft.AspNetCore.Mvc.JsonResult> e <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="b8bd2-114">Le azioni possono restituire risultati formattati in un formato specifico, indipendentemente dalle preferenze dei client.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="b8bd2-115">Ad esempio, la restituzione di `JsonResult` restituisce dati in formato JSON.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="b8bd2-116">La restituzione `ContentResult` di o di una stringa restituisce dati di stringa in formato testo normale.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="b8bd2-117">Non è necessario eseguire un'azione per restituire un tipo specifico.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="b8bd2-118">ASP.NET Core supporta qualsiasi valore restituito da un oggetto.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="b8bd2-119">I risultati di azioni che restituiscono oggetti che non sono <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipi vengono serializzati usando l' <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementazione appropriata.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="b8bd2-120">Per altre informazioni, vedere <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="b8bd2-121">Il metodo helper incorporato <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> restituisce dati in formato JSON:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="b8bd2-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="b8bd2-122">Il download di esempio restituisce l'elenco degli autori.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="b8bd2-123">Utilizzando gli strumenti di sviluppo del browser [F12 o l'](https://www.getpostman.com/tools) autore del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="b8bd2-124">Viene visualizzata l'intestazione della risposta contenente **Content-Type:** `application/json; charset=utf-8` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="b8bd2-125">Verranno visualizzate le intestazioni della richiesta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-125">The request headers are displayed.</span></span> <span data-ttu-id="b8bd2-126">Ad esempio, l' `Accept` intestazione.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-126">For example, the `Accept` header.</span></span> <span data-ttu-id="b8bd2-127">L' `Accept` intestazione viene ignorata dal codice precedente.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="b8bd2-128">Per restituire dati in formato testo normale, usare <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> e l'helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content>:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="b8bd2-129">Nel codice precedente, l'oggetto `Content-Type` restituito è `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="b8bd2-130">La restituzione di una stringa fornisce `Content-Type` `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="b8bd2-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="b8bd2-131">Per le azioni con più tipi restituiti, restituire `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="b8bd2-132">Ad esempio, la restituzione di codici di stato HTTP diversi in base al risultato delle operazioni eseguite.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="b8bd2-133">Negoziazione del contenuto</span><span class="sxs-lookup"><span data-stu-id="b8bd2-133">Content negotiation</span></span>

<span data-ttu-id="b8bd2-134">La negoziazione del contenuto si verifica quando il client specifica un' [intestazione Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="b8bd2-135">Il formato predefinito usato da ASP.NET Core è [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="b8bd2-136">Negoziazione del contenuto:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-136">Content negotiation is:</span></span>

* <span data-ttu-id="b8bd2-137">Implementato da <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="b8bd2-138">Incorporata nei risultati dell'azione specifici del codice di stato restituiti dai metodi helper.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="b8bd2-139">I metodi helper dei risultati delle azioni sono basati su `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="b8bd2-140">Quando viene restituito un tipo di modello, il tipo restituito è `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="b8bd2-141">Il metodo di azione seguente usa i metodi helper `Ok` e `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="b8bd2-142">Per impostazione predefinita, ASP.NET Core supporta i `application/json` `text/json` tipi di supporto, e `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="b8bd2-143">Gli strumenti, ad esempio [Fiddler](https://www.telerik.com/fiddler) o [post](https://www.getpostman.com/tools) , possono impostare l' `Accept` intestazione della richiesta per specificare il formato restituito.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="b8bd2-144">Quando l' `Accept` intestazione contiene un tipo supportato dal server, viene restituito tale tipo.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="b8bd2-145">Nella sezione successiva viene illustrato come aggiungere formattatori aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="b8bd2-146">Le azioni del controller possono restituire POCO (Plain Old CLR Objects).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="b8bd2-147">Quando viene restituito un oggetto POCO, il runtime crea automaticamente un `ObjectResult` oggetto che esegue il wrapping dell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="b8bd2-148">Il client ottiene l'oggetto serializzato formattato.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="b8bd2-149">Se l'oggetto restituito è `null` , `204 No Content` viene restituita una risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="b8bd2-150">Restituzione di un tipo di oggetto:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="b8bd2-151">Nel codice precedente, una richiesta di un alias autore valido restituisce una `200 OK` risposta con i dati dell'autore.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="b8bd2-152">Una richiesta di un alias non valido restituisce una `204 No Content` risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="b8bd2-153">Intestazione Accept</span><span class="sxs-lookup"><span data-stu-id="b8bd2-153">The Accept header</span></span>

<span data-ttu-id="b8bd2-154">La *negoziazione* del contenuto si verifica quando `Accept` viene visualizzata un'intestazione nella richiesta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="b8bd2-155">Quando una richiesta contiene un'intestazione Accept, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="b8bd2-156">Enumera i tipi di supporto nell'intestazione Accept in ordine di preferenza.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="b8bd2-157">Tenta di trovare un formattatore in grado di generare una risposta in uno dei formati specificati.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="b8bd2-158">Se non viene trovato alcun formattatore in grado di soddisfare la richiesta del client, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="b8bd2-159">Restituisce `406 Not Acceptable` se <xref:Microsoft.AspNetCore.Mvc.MvcOptions> è stato impostato oppure-</span><span class="sxs-lookup"><span data-stu-id="b8bd2-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="b8bd2-160">Tenta di trovare il primo formattatore in grado di generare una risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="b8bd2-161">Se non è configurato alcun formattatore per il formato richiesto, viene utilizzato il primo formattatore in grado di formattare l'oggetto.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="b8bd2-162">Se `Accept` nella richiesta non è presente alcuna intestazione:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="b8bd2-163">Il primo formattatore in grado di gestire l'oggetto viene utilizzato per serializzare la risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="b8bd2-164">Non si verifica alcuna negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="b8bd2-165">Il server sta determinando il formato da restituire.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-165">The server is determining what format to return.</span></span>

<span data-ttu-id="b8bd2-166">Se l'intestazione Accept contiene `*/*` , l'intestazione viene ignorata, a meno che non `RespectBrowserAcceptHeader` sia impostato su true in <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="b8bd2-167">Browser e negoziazione del contenuto</span><span class="sxs-lookup"><span data-stu-id="b8bd2-167">Browsers and content negotiation</span></span>

<span data-ttu-id="b8bd2-168">A differenza dei client API tipici, i Web browser forniscono le `Accept` intestazioni.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="b8bd2-169">Web browser specificare molti formati, inclusi i caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="b8bd2-170">Per impostazione predefinita, quando il Framework rileva che la richiesta viene ricevuta da un browser:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="b8bd2-171">L' `Accept` intestazione viene ignorata.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="b8bd2-172">Il contenuto viene restituito in JSON, a meno che non sia configurato diversamente.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="b8bd2-173">Ciò offre un'esperienza più coerente nei browser quando si utilizzano le API.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="b8bd2-174">Per configurare un'app in modo da rispettare le intestazioni Accept del browser, impostare <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> su `true` :</span><span class="sxs-lookup"><span data-stu-id="b8bd2-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="b8bd2-175">Configura formattatori</span><span class="sxs-lookup"><span data-stu-id="b8bd2-175">Configure formatters</span></span>

<span data-ttu-id="b8bd2-176">Le app che devono supportare formati aggiuntivi possono aggiungere i pacchetti NuGet appropriati e configurare il supporto.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="b8bd2-177">Esistono formattatori separati per input e output.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="b8bd2-178">I formattatori di input vengono utilizzati dall' [associazione di modelli](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b8bd2-179">I formattatori di output vengono utilizzati per formattare le risposte.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="b8bd2-180">Per informazioni sulla creazione di un formattatore personalizzato, vedere [formattatori personalizzati](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="b8bd2-181">Aggiungere il supporto per il formato XML</span><span class="sxs-lookup"><span data-stu-id="b8bd2-181">Add XML format support</span></span>

<span data-ttu-id="b8bd2-182">I formattatori XML implementati usando <xref:System.Xml.Serialization.XmlSerializer> vengono configurati chiamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="b8bd2-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="b8bd2-183">Il codice precedente serializza i risultati usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b8bd2-184">Quando si usa il codice precedente, i metodi del controller restituiscono il formato appropriato in base all'intestazione della richiesta `Accept` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="b8bd2-185">Configurare i formattatori basati su System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="b8bd2-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="b8bd2-186">Le funzionalità per i formattatori basati su `System.Text.Json` possono essere configurate tramite `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b8bd2-187">È possibile configurare le opzioni di serializzazione dell'output, in base alle singole azioni, usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b8bd2-188">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="b8bd2-189">Aggiungere il supporto del formato JSON basato su Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="b8bd2-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="b8bd2-190">Prima di ASP.NET Core 3,0, i formattatori JSON usati per impostazione predefinita venivano implementati utilizzando il `Newtonsoft.Json` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="b8bd2-191">In ASP.NET Core 3.0 o versione successiva, i formattatori JSON predefiniti sono basati su `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="b8bd2-192">Il supporto per `Newtonsoft.Json` formattatori e funzionalità basati è disponibile installando il [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) pacchetto NuGet e configurarlo in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="b8bd2-193">Alcune funzionalità potrebbero non funzionare correttamente con `System.Text.Json` formattatori basati su e richiedono un riferimento ai `Newtonsoft.Json` formattatori basati su.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="b8bd2-194">Continuare a usare i `Newtonsoft.Json` formattatori basati su se l'app:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="b8bd2-195">Utilizza `Newtonsoft.Json` gli attributi.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="b8bd2-196">Ad esempio, `[JsonProperty]` o `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="b8bd2-197">Personalizza le impostazioni di serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="b8bd2-198">Si basa sulle funzionalità `Newtonsoft.Json` fornite da.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="b8bd2-199">Configura `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="b8bd2-200">Prima di ASP.NET Core 3.0 `JsonResult.SerializerSettings` accetta un'istanza di `JsonSerializerSettings` specifica di `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="b8bd2-201">Genera documentazione [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="b8bd2-202">Le funzionalità per i `Newtonsoft.Json` formattatori basati su possono essere configurate utilizzando `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="b8bd2-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b8bd2-203">È possibile configurare le opzioni di serializzazione dell'output, in base alle singole azioni, usando `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b8bd2-204">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="b8bd2-205">Aggiungere il supporto per il formato XML</span><span class="sxs-lookup"><span data-stu-id="b8bd2-205">Add XML format support</span></span>

<span data-ttu-id="b8bd2-206">Per la formattazione XML è necessario il pacchetto NuGet [Microsoft. AspNetCore. Mvc. Formatters. XML](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="b8bd2-207">I formattatori XML implementati usando <xref:System.Xml.Serialization.XmlSerializer> vengono configurati chiamando <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="b8bd2-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="b8bd2-208">Il codice precedente serializza i risultati usando `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b8bd2-209">Quando si usa il codice precedente, i metodi del controller devono restituire il formato appropriato in base all'intestazione della richiesta `Accept` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="b8bd2-210">Specificare un formato</span><span class="sxs-lookup"><span data-stu-id="b8bd2-210">Specify a format</span></span>

<span data-ttu-id="b8bd2-211">Per limitare i formati di risposta, applicare il [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="b8bd2-212">Come la maggior parte dei [filtri](xref:mvc/controllers/filters), `[Produces]` può essere applicato all'azione, al controller o all'ambito globale:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="b8bd2-213">Il [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtro precedente:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="b8bd2-214">Impone a tutte le azioni all'interno del controller di restituire risposte in formato JSON.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="b8bd2-215">Se sono configurati altri formattatori e il client specifica un formato diverso, viene restituito JSON.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="b8bd2-216">Per ulteriori informazioni, vedere [filtri](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="b8bd2-217">Formattatori case speciali</span><span class="sxs-lookup"><span data-stu-id="b8bd2-217">Special case formatters</span></span>

<span data-ttu-id="b8bd2-218">Alcuni casi speciali vengono implementati tramite formattatori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="b8bd2-219">Per impostazione predefinita, `string` i tipi restituiti vengono formattati come *testo/normale* (*testo/HTML* se richiesto tramite l' `Accept` intestazione).</span><span class="sxs-lookup"><span data-stu-id="b8bd2-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="b8bd2-220">Questo comportamento può essere eliminato rimuovendo <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="b8bd2-221">I formattatori vengono rimossi nel `ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="b8bd2-222">Le azioni con un tipo restituito dall'oggetto modello restituiscono quando viene restituito `204 No Content` `null` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="b8bd2-223">Questo comportamento può essere eliminato rimuovendo <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="b8bd2-224">Il codice seguente rimuove `StringOutputFormatter` e `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="b8bd2-225">Senza `StringOutputFormatter` , il formattatore JSON incorporato formatta i `string` tipi restituiti.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="b8bd2-226">Se il formattatore JSON incorporato viene rimosso ed è disponibile un formattatore XML, il formattatore XML formatta i `string` tipi restituiti.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="b8bd2-227">In caso contrario, i `string` tipi restituiti restituiscono `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="b8bd2-228">Senza `HttpNoContentOutputFormatter`, gli oggetti Null vengono formattati tramite il formattatore configurato.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="b8bd2-229">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-229">For example:</span></span>

* <span data-ttu-id="b8bd2-230">Il formattatore JSON restituisce una risposta con un corpo di `null` .</span><span class="sxs-lookup"><span data-stu-id="b8bd2-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="b8bd2-231">Il formattatore XML restituisce un elemento XML vuoto con l'attributo `xsi:nil="true"` impostato.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="b8bd2-232">Mapping URL formato risposta</span><span class="sxs-lookup"><span data-stu-id="b8bd2-232">Response format URL mappings</span></span>

<span data-ttu-id="b8bd2-233">I client possono richiedere un particolare formato come parte dell'URL, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="b8bd2-234">Nella stringa di query o in una parte del percorso.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="b8bd2-235">Utilizzando un'estensione di file specifica del formato, ad esempio XML o JSON.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="b8bd2-236">Il mapping dal percorso della richiesta deve essere specificato nella route usata dall'API.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="b8bd2-237">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="b8bd2-238">La route precedente consente di specificare il formato richiesto come estensione di file facoltativa.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="b8bd2-239">L' [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attributo controlla l'esistenza del valore di formato in `RouteData` ed esegue il mapping del formato della risposta al formattatore appropriato al momento della creazione della risposta.</span><span class="sxs-lookup"><span data-stu-id="b8bd2-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="b8bd2-240">Route</span><span class="sxs-lookup"><span data-stu-id="b8bd2-240">Route</span></span>        |             <span data-ttu-id="b8bd2-241">Formattatore</span><span class="sxs-lookup"><span data-stu-id="b8bd2-241">Formatter</span></span>              |
|---
<span data-ttu-id="b8bd2-242">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-243">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-244">'Identity'</span></span>
- <span data-ttu-id="b8bd2-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-246">'Razor'</span></span>
- <span data-ttu-id="b8bd2-247">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-248">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-249">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-250">'Identity'</span></span>
- <span data-ttu-id="b8bd2-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-252">'Razor'</span></span>
- <span data-ttu-id="b8bd2-253">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-254">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-255">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-256">'Identity'</span></span>
- <span data-ttu-id="b8bd2-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-258">'Razor'</span></span>
- <span data-ttu-id="b8bd2-259">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-260">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-261">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-262">'Identity'</span></span>
- <span data-ttu-id="b8bd2-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-264">'Razor'</span></span>
- <span data-ttu-id="b8bd2-265">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-266">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-267">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-268">'Identity'</span></span>
- <span data-ttu-id="b8bd2-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-270">'Razor'</span></span>
- <span data-ttu-id="b8bd2-271">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-272">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-273">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-274">'Identity'</span></span>
- <span data-ttu-id="b8bd2-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-276">'Razor'</span></span>
- <span data-ttu-id="b8bd2-277">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-278">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-279">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-280">'Identity'</span></span>
- <span data-ttu-id="b8bd2-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-282">'Razor'</span></span>
- <span data-ttu-id="b8bd2-283">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-284">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-285">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-286">'Identity'</span></span>
- <span data-ttu-id="b8bd2-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-288">'Razor'</span></span>
- <span data-ttu-id="b8bd2-289">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-290">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-291">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-292">'Identity'</span></span>
- <span data-ttu-id="b8bd2-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-294">'Razor'</span></span>
- <span data-ttu-id="b8bd2-295">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-296">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-297">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-298">'Identity'</span></span>
- <span data-ttu-id="b8bd2-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-300">'Razor'</span></span>
- <span data-ttu-id="b8bd2-301">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-301">'SignalR' uid:</span></span> 

<span data-ttu-id="b8bd2-302">------------|---title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-303">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-304">'Identity'</span></span>
- <span data-ttu-id="b8bd2-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-306">'Razor'</span></span>
- <span data-ttu-id="b8bd2-307">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-308">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-309">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-310">'Identity'</span></span>
- <span data-ttu-id="b8bd2-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-312">'Razor'</span></span>
- <span data-ttu-id="b8bd2-313">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-314">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-315">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-316">'Identity'</span></span>
- <span data-ttu-id="b8bd2-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-318">'Razor'</span></span>
- <span data-ttu-id="b8bd2-319">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-320">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-321">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-322">'Identity'</span></span>
- <span data-ttu-id="b8bd2-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-324">'Razor'</span></span>
- <span data-ttu-id="b8bd2-325">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-326">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-327">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-328">'Identity'</span></span>
- <span data-ttu-id="b8bd2-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-330">'Razor'</span></span>
- <span data-ttu-id="b8bd2-331">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-332">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-333">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-334">'Identity'</span></span>
- <span data-ttu-id="b8bd2-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-336">'Razor'</span></span>
- <span data-ttu-id="b8bd2-337">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-338">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-339">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-340">'Identity'</span></span>
- <span data-ttu-id="b8bd2-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-342">'Razor'</span></span>
- <span data-ttu-id="b8bd2-343">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-344">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-345">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-346">'Identity'</span></span>
- <span data-ttu-id="b8bd2-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-348">'Razor'</span></span>
- <span data-ttu-id="b8bd2-349">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-350">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-351">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-352">'Identity'</span></span>
- <span data-ttu-id="b8bd2-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-354">'Razor'</span></span>
- <span data-ttu-id="b8bd2-355">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-356">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-357">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-358">'Identity'</span></span>
- <span data-ttu-id="b8bd2-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-360">'Razor'</span></span>
- <span data-ttu-id="b8bd2-361">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-362">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-363">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-364">'Identity'</span></span>
- <span data-ttu-id="b8bd2-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-366">'Razor'</span></span>
- <span data-ttu-id="b8bd2-367">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-368">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-369">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-370">'Identity'</span></span>
- <span data-ttu-id="b8bd2-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-372">'Razor'</span></span>
- <span data-ttu-id="b8bd2-373">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-374">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-375">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-376">'Identity'</span></span>
- <span data-ttu-id="b8bd2-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-378">'Razor'</span></span>
- <span data-ttu-id="b8bd2-379">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-380">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-381">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-382">'Identity'</span></span>
- <span data-ttu-id="b8bd2-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-384">'Razor'</span></span>
- <span data-ttu-id="b8bd2-385">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-386">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-387">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-388">'Identity'</span></span>
- <span data-ttu-id="b8bd2-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-390">'Razor'</span></span>
- <span data-ttu-id="b8bd2-391">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="b8bd2-392">title: autore: Descrizione: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="b8bd2-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b8bd2-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-393">'Blazor'</span></span>
- <span data-ttu-id="b8bd2-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-394">'Identity'</span></span>
- <span data-ttu-id="b8bd2-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="b8bd2-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b8bd2-396">'Razor'</span></span>
- <span data-ttu-id="b8bd2-397">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="b8bd2-397">'SignalR' uid:</span></span> 

<span data-ttu-id="b8bd2-398">------------------| |   `/api/products/5`    |    Formattatore di output predefinito | | `/api/products/5.json` | Formattatore JSON (se configurato) | | `/api/products/5.xml`  | Formattatore XML (se configurato) |</span><span class="sxs-lookup"><span data-stu-id="b8bd2-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
