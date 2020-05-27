---
<span data-ttu-id="ec35e-101">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-102">'Blazor'</span></span>
- <span data-ttu-id="ec35e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-103">'Identity'</span></span>
- <span data-ttu-id="ec35e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-105">'Razor'</span></span>
- <span data-ttu-id="ec35e-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-106">'SignalR' uid:</span></span> 

---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="ec35e-107">Routing ad azioni del controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ec35e-107">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="ec35e-108">Di [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec35e-108">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ec35e-109">I controller di ASP.NET Core usano il [middleware](xref:fundamentals/middleware/index) di routing per trovare le corrispondenze con gli URL delle richieste in ingresso ed eseguirne il mapping alle [azioni](#action).</span><span class="sxs-lookup"><span data-stu-id="ec35e-109">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="ec35e-110">Modelli di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-110">Routes templates:</span></span>

* <span data-ttu-id="ec35e-111">Sono definiti nel codice di avvio o negli attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-111">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="ec35e-112">Descrizione della corrispondenza dei percorsi URL con le [azioni](#action).</span><span class="sxs-lookup"><span data-stu-id="ec35e-112">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="ec35e-113">Vengono usati per generare URL per i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-113">Are used to generate URLs for links.</span></span> <span data-ttu-id="ec35e-114">I collegamenti generati vengono in genere restituiti nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="ec35e-114">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="ec35e-115">Le azioni sono indirizzate [convenzionalmente](#cr) o [indirizzate agli attributi](#ar).</span><span class="sxs-lookup"><span data-stu-id="ec35e-115">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="ec35e-116">L'inserimento di una route sul controller o sull' [azione](#action) lo rende instradato con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-116">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="ec35e-117">Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="ec35e-117">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ec35e-118">Questo documento:</span><span class="sxs-lookup"><span data-stu-id="ec35e-118">This document:</span></span>

* <span data-ttu-id="ec35e-119">Illustra le interazioni tra MVC e il routing:</span><span class="sxs-lookup"><span data-stu-id="ec35e-119">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="ec35e-120">Il modo in cui le app MVC tipiche usano le funzionalità di routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-120">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="ec35e-121">Vengono illustrati entrambi:</span><span class="sxs-lookup"><span data-stu-id="ec35e-121">Covers both:</span></span>
    * <span data-ttu-id="ec35e-122">[Routing convenzionale](#cr) usato in genere con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-122">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="ec35e-123">*Routing degli attributi* usato con le API REST.</span><span class="sxs-lookup"><span data-stu-id="ec35e-123">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="ec35e-124">Se si è interessati principalmente al routing per le API REST, passare alla sezione [routing degli attributi per le API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-124">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="ec35e-125">Vedere [routing](xref:fundamentals/routing) per i dettagli del routing avanzato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-125">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="ec35e-126">Si riferisce al sistema di routing predefinito aggiunto in ASP.NET Core 3,0, denominato endpoint routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-126">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="ec35e-127">È possibile usare i controller con la versione precedente di routing per motivi di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="ec35e-127">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="ec35e-128">Per istruzioni, vedere la [Guida alla migrazione 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-128">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="ec35e-129">Per materiale di riferimento sul sistema di routing legacy, vedere la [versione 2,2 di questo documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-129">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="ec35e-130">Configurare la route convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-130">Set up conventional route</span></span>

<span data-ttu-id="ec35e-131">`Startup.Configure`Quando si usa il [routing convenzionale](#crd), in genere il codice è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-131">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="ec35e-132">All'interno della chiamata a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> viene usato per creare una singola route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-132">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> is used to create a single route.</span></span> <span data-ttu-id="ec35e-133">La route singola è denominata `default` Route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-133">The single route is named `default` route.</span></span> <span data-ttu-id="ec35e-134">La maggior parte delle app con controller e visualizzazioni usa un modello di route simile a quello della `default` Route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-134">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="ec35e-135">Le API REST devono usare il [routing degli attributi](#ar).</span><span class="sxs-lookup"><span data-stu-id="ec35e-135">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="ec35e-136">Il modello di route `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-136">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="ec35e-137">Corrisponde a un percorso URL come`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="ec35e-137">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="ec35e-138">Estrae i valori della route `{ controller = Products, action = Details, id = 5 }` suddivisione in token il percorso.</span><span class="sxs-lookup"><span data-stu-id="ec35e-138">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ec35e-139">L'estrazione dei valori di route comporta una corrispondenza se l'app dispone di un controller denominato `ProductsController` e di un' `Details` azione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-139">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="ec35e-140">`/Products/Details/5`il modello associa il valore di `id = 5` per impostare il `id` parametro su `5` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-140">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="ec35e-141">Per ulteriori informazioni, vedere [associazione di modelli](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-141">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="ec35e-142">`{controller=Home}`definisce `Home` come valore predefinito `controller` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-142">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="ec35e-143">`{action=Index}`definisce `Index` come valore predefinito `action` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-143">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="ec35e-144">Il `?` carattere in `{id?}` definisce `id` come facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-144">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="ec35e-145">I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-145">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ec35e-146">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-146">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="ec35e-147">Corrisponde al percorso dell'URL `/` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-147">Matches the URL path `/`.</span></span>
* <span data-ttu-id="ec35e-148">Produce i valori della route `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-148">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="ec35e-149">I valori per `controller` e `action` usano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-149">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="ec35e-150">`id`non produce un valore poiché non esiste un segmento corrispondente nel percorso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-150">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ec35e-151">`/`corrisponde solo se esiste un' `HomeController` azione e `Index` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-151">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ec35e-152">Utilizzando la definizione del controller e il modello di route precedenti, l' `HomeController.Index` azione viene eseguita per i seguenti percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-152">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="ec35e-153">Il percorso URL `/` Usa i controller e l'azione predefiniti del modello `Home` di route `Index` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-153">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="ec35e-154">Il percorso URL `/Home` Usa l'azione predefinita del modello di route `Index` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-154">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="ec35e-155">Il metodo pratico <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span><span class="sxs-lookup"><span data-stu-id="ec35e-155">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="ec35e-156">Sostituisce</span><span class="sxs-lookup"><span data-stu-id="ec35e-156">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="ec35e-157">Il routing viene configurato usando <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> il <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware e.</span><span class="sxs-lookup"><span data-stu-id="ec35e-157">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> middleware.</span></span> <span data-ttu-id="ec35e-158">Per usare i controller:</span><span class="sxs-lookup"><span data-stu-id="ec35e-158">To use controllers:</span></span>
>
> * <span data-ttu-id="ec35e-159">Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> all'interno per eseguire il mapping tra i `UseEndpoints` controller [indirizzati degli attributi](#ar) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-159">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="ec35e-160">Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> o <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> per eseguire il mapping di controller [indirizzati convenzionalmente](#cr) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-160">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>, to map [conventionally routed](#cr) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ec35e-161">Routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-161">Conventional routing</span></span>

<span data-ttu-id="ec35e-162">Il routing convenzionale viene usato con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-162">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="ec35e-163">La route predefinita (`default`):</span><span class="sxs-lookup"><span data-stu-id="ec35e-163">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="ec35e-164">è un esempio di *routing convenzionale*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-164">is an example of a *conventional routing*.</span></span> <span data-ttu-id="ec35e-165">Viene chiamato *routing convenzionale* perché stabilisce una *convenzione* per i percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-165">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ec35e-166">Il primo segmento di percorso, `{controller=Home}` , esegue il mapping al nome del controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-166">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="ec35e-167">Il secondo segmento, `{action=Index}` , esegue il mapping al nome dell' [azione](#action) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-167">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="ec35e-168">Il terzo segmento `{id?}` viene usato per un oggetto facoltativo `id` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-168">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="ec35e-169">`?`In lo `{id?}` rende facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-169">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="ec35e-170">`id`viene utilizzato per eseguire il mapping a un'entità del modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-170">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="ec35e-171">Utilizzando questa `default` Route, il percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-171">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="ec35e-172">`/Products/List`esegue il mapping all' `ProductsController.List` azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-172">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="ec35e-173">`/Blog/Article/17`esegue il mapping a `BlogController.Article` e in genere il modello associa il `id` parametro a 17.</span><span class="sxs-lookup"><span data-stu-id="ec35e-173">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="ec35e-174">Questo mapping:</span><span class="sxs-lookup"><span data-stu-id="ec35e-174">This mapping:</span></span>

* <span data-ttu-id="ec35e-175">Si basa **solo**sui nomi del controller e dell' [azione](#action) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-175">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="ec35e-176">Non è basato su spazi dei nomi, percorsi di file di origine o parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-176">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="ec35e-177">L'uso del routing convenzionale con la route predefinita consente di creare l'app senza dover ottenere un nuovo modello di URL per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-177">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="ec35e-178">Per un'app con azioni di stile [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , con coerenza per gli URL tra i controller:</span><span class="sxs-lookup"><span data-stu-id="ec35e-178">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="ec35e-179">Consente di semplificare il codice.</span><span class="sxs-lookup"><span data-stu-id="ec35e-179">Helps simplify the code.</span></span>
* <span data-ttu-id="ec35e-180">Rende l'interfaccia utente più prevedibile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-180">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ec35e-181">`id`Nel codice precedente viene definito come facoltativo dal modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-181">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="ec35e-182">Le azioni possono essere eseguite senza l'ID facoltativo fornito come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-182">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="ec35e-183">In genere, quando `id` viene omesso dall'URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-183">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="ec35e-184">`id`viene impostato su `0` dall'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="ec35e-184">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="ec35e-185">Non è stata trovata alcuna entità nel database corrispondente `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-185">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="ec35e-186">Il [routing degli attributi](#ar) offre un controllo con granularità fine per rendere l'ID necessario per alcune azioni e non per altri.</span><span class="sxs-lookup"><span data-stu-id="ec35e-186">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ec35e-187">Per convenzione, la documentazione include parametri facoltativi come `id` quando è probabile che vengano visualizzati nell'utilizzo corretto.</span><span class="sxs-lookup"><span data-stu-id="ec35e-187">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="ec35e-188">La maggior parte delle app dovrebbe scegliere uno schema di routing semplice e descrittivo in modo che gli URL siano leggibili e significativi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-188">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="ec35e-189">La route convenzionale predefinita `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-189">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="ec35e-190">Supporta uno schema di routing semplice e descrittivo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-190">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="ec35e-191">È un punto iniziale utile per le app basate su interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-191">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="ec35e-192">È l'unico modello di route necessario per molte app dell'interfaccia utente Web.</span><span class="sxs-lookup"><span data-stu-id="ec35e-192">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="ec35e-193">Per le app di interfaccia utente Web di dimensioni maggiori, un'altra route che usa le [aree](#areas) se di frequente è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-193">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="ec35e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span><span class="sxs-lookup"><span data-stu-id="ec35e-194"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :</span></span>

* <span data-ttu-id="ec35e-195">Assegnare automaticamente un valore di **ordine** ai rispettivi endpoint in base all'ordine in cui vengono richiamati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-195">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="ec35e-196">Routing degli endpoint in ASP.NET Core 3,0 e versioni successive:</span><span class="sxs-lookup"><span data-stu-id="ec35e-196">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="ec35e-197">Non ha un concetto di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-197">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="ec35e-198">Non fornisce garanzie di ordinamento per l'esecuzione dell'estensibilità, tutti gli endpoint vengono elaborati contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-198">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="ec35e-199">Abilitare la [registrazione](xref:fundamentals/logging/index) per verificare in che modo le implementazioni del routing predefinite, ad esempio <xref:Microsoft.AspNetCore.Routing.Route>, corrispondono alle richieste.</span><span class="sxs-lookup"><span data-stu-id="ec35e-199">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="ec35e-200">Il [routing degli attributi](#ar) è illustrato più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-200">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="ec35e-201">Più route convenzionali</span><span class="sxs-lookup"><span data-stu-id="ec35e-201">Multiple conventional routes</span></span>

<span data-ttu-id="ec35e-202">È possibile aggiungere più [Route convenzionali](#cr) all'interno `UseEndpoints` di aggiungendo altre chiamate a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> .</span><span class="sxs-lookup"><span data-stu-id="ec35e-202">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>.</span></span> <span data-ttu-id="ec35e-203">In questo modo è possibile definire più convenzioni o aggiungere route convenzionali dedicate a un' [azione](#action)specifica, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ec35e-203">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="ec35e-204">La `blog` Route nel codice precedente è una **Route convenzionale dedicata**.</span><span class="sxs-lookup"><span data-stu-id="ec35e-204">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="ec35e-205">Viene chiamato Route convenzionale dedicata perché:</span><span class="sxs-lookup"><span data-stu-id="ec35e-205">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="ec35e-206">Usa il [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="ec35e-206">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="ec35e-207">È dedicata a un' [azione](#action)specifica.</span><span class="sxs-lookup"><span data-stu-id="ec35e-207">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="ec35e-208">Poiché `controller` e `action` non vengono visualizzati nel modello di route `"blog/{*article}"` come parametri:</span><span class="sxs-lookup"><span data-stu-id="ec35e-208">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="ec35e-209">Possono avere solo i valori predefiniti `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-209">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="ec35e-210">Questa route è sempre associata all'azione `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-210">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ec35e-211">`/Blog`, `/Blog/Article` e `/Blog/{any-string}` sono gli unici percorsi URL che corrispondono alla route del Blog.</span><span class="sxs-lookup"><span data-stu-id="ec35e-211">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="ec35e-212">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-212">The preceding example:</span></span>

* <span data-ttu-id="ec35e-213">`blog`la route ha una priorità più elevata per le corrispondenze rispetto alla `default` Route perché viene aggiunta per prima.</span><span class="sxs-lookup"><span data-stu-id="ec35e-213">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="ec35e-214">È un esempio di routing in stile [Slug](https://developer.mozilla.org/docs/Glossary/Slug) in cui è tipico avere un nome di articolo come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-214">Is and example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="ec35e-215">In ASP.NET Core 3,0 e versioni successive, il routing non:</span><span class="sxs-lookup"><span data-stu-id="ec35e-215">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="ec35e-216">Definire un concetto denominato *Route*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-216">Define a concept called a *route*.</span></span> <span data-ttu-id="ec35e-217">`UseRouting`aggiunge la corrispondenza della route alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="ec35e-217">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="ec35e-218">Il `UseRouting` middleware esamina il set di endpoint definiti nell'app e seleziona la corrispondenza migliore per l'endpoint in base alla richiesta.</span><span class="sxs-lookup"><span data-stu-id="ec35e-218">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="ec35e-219">Fornire garanzie sull'ordine di esecuzione dell'estensibilità <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , ad esempio o <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="ec35e-219">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="ec35e-220">Vedere [routing](xref:fundamentals/routing) per il materiale di riferimento sul routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-220">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="ec35e-221">Ordine di routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-221">Conventional routing order</span></span>

<span data-ttu-id="ec35e-222">Il routing convenzionale corrisponde solo a una combinazione di azione e controller definiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="ec35e-222">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="ec35e-223">Questa operazione è progettata per semplificare i casi in cui le route convenzionali si sovrappongono.</span><span class="sxs-lookup"><span data-stu-id="ec35e-223">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="ec35e-224">Se si aggiungono route usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*> e si <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> assegna automaticamente un valore di ordine ai rispettivi endpoint in base all'ordine in cui vengono richiamati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-224">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="ec35e-225">Corrisponde a una route visualizzata in precedenza con una priorità più alta.</span><span class="sxs-lookup"><span data-stu-id="ec35e-225">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="ec35e-226">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-226">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ec35e-227">In generale, le route con aree devono essere posizionate in precedenza perché sono più specifiche delle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-227">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="ec35e-228">Le route [convenzionali dedicate](#dcr) con parametri di route catch-all come `{*article}` possono rendere troppo [greedy](xref:fundamentals/routing#greedy)una route, vale a dire che corrisponde agli URL per i quali si intende trovare una corrispondenza con altre route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-228">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ec35e-229">Inserire le route greedy in un secondo momento nella tabella di route per evitare corrispondenze greedy.</span><span class="sxs-lookup"><span data-stu-id="ec35e-229">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="ec35e-230">Risoluzione di azioni ambigue</span><span class="sxs-lookup"><span data-stu-id="ec35e-230">Resolving ambiguous actions</span></span>

<span data-ttu-id="ec35e-231">Quando due endpoint corrispondono tramite il routing, il routing deve eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="ec35e-231">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="ec35e-232">Scegliere il candidato migliore.</span><span class="sxs-lookup"><span data-stu-id="ec35e-232">Choose the best candidate.</span></span>
* <span data-ttu-id="ec35e-233">Generazione di un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-233">Throw an exception.</span></span>

<span data-ttu-id="ec35e-234">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ec35e-234">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="ec35e-235">Il controller precedente definisce due azioni che corrispondono a:</span><span class="sxs-lookup"><span data-stu-id="ec35e-235">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="ec35e-236">Percorso URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="ec35e-236">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="ec35e-237">Dati di route `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-237">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="ec35e-238">Si tratta di un modello tipico per i controller MVC:</span><span class="sxs-lookup"><span data-stu-id="ec35e-238">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="ec35e-239">`Edit(int)`Visualizza un modulo per la modifica di un prodotto.</span><span class="sxs-lookup"><span data-stu-id="ec35e-239">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="ec35e-240">`Edit(int, Product)`elabora il form inviato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-240">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="ec35e-241">Per risolvere la route corretta:</span><span class="sxs-lookup"><span data-stu-id="ec35e-241">To resolve the correct route:</span></span>

* <span data-ttu-id="ec35e-242">`Edit(int, Product)`viene selezionato quando la richiesta è un HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-242">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="ec35e-243">`Edit(int)`viene selezionato quando il [verbo http](#verb) è qualsiasi altro elemento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-243">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="ec35e-244">`Edit(int)`viene in genere chiamato tramite `GET` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-244">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="ec35e-245">L'oggetto <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , viene fornito al routing in modo che sia possibile scegliere in base al metodo HTTP della richiesta.</span><span class="sxs-lookup"><span data-stu-id="ec35e-245">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="ec35e-246">`HttpPostAttribute`Consente `Edit(int, Product)` di ottenere una corrispondenza migliore rispetto a `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-246">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="ec35e-247">È importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-247">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="ec35e-248">Attributi simili vengono definiti per altri [verbi HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="ec35e-248">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="ec35e-249">Nel [routing convenzionale](#cr), è comune per le azioni usare lo stesso nome di azione quando fanno parte di un flusso di lavoro di invio del modulo di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-249">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="ec35e-250">Vedere ad esempio [esaminare i due metodi di azione di modifica](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="ec35e-250">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="ec35e-251">Se il routing non è in grado di scegliere un candidato migliore, <xref:System.Reflection.AmbiguousMatchException> viene generata un'eccezione, che elenca più endpoint corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-251">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="ec35e-252">Nomi di route convenzionali</span><span class="sxs-lookup"><span data-stu-id="ec35e-252">Conventional route names</span></span>

<span data-ttu-id="ec35e-253">Le stringhe `"blog"` e `"default"` negli esempi seguenti sono nomi di route convenzionali:</span><span class="sxs-lookup"><span data-stu-id="ec35e-253">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ec35e-254">I nomi di route assegnano alla route un nome logico.</span><span class="sxs-lookup"><span data-stu-id="ec35e-254">The route names give the route a logical name.</span></span> <span data-ttu-id="ec35e-255">La route denominata può essere usata per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-255">The named route can be used for URL generation.</span></span> <span data-ttu-id="ec35e-256">L'uso di una route denominata semplifica la creazione di URL quando l'ordinamento delle route può rendere complessa la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-256">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ec35e-257">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-257">Route names must be unique application wide.</span></span>

<span data-ttu-id="ec35e-258">Nomi di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-258">Route names:</span></span>

* <span data-ttu-id="ec35e-259">Non hanno alcun effetto sulla corrispondenza degli URL o sulla gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="ec35e-259">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="ec35e-260">Vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-260">Are used only for URL generation.</span></span>

<span data-ttu-id="ec35e-261">Il concetto di nome della route è rappresentato nel routing come [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="ec35e-261">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="ec35e-262">Il **nome della route** e il nome dell' **endpoint**:</span><span class="sxs-lookup"><span data-stu-id="ec35e-262">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="ec35e-263">Sono intercambiabili.</span><span class="sxs-lookup"><span data-stu-id="ec35e-263">Are interchangeable.</span></span>
* <span data-ttu-id="ec35e-264">Quello usato nella documentazione e nel codice dipende dall'API descritta.</span><span class="sxs-lookup"><span data-stu-id="ec35e-264">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="ec35e-265">Routing degli attributi per le API REST</span><span class="sxs-lookup"><span data-stu-id="ec35e-265">Attribute routing for REST APIs</span></span>

<span data-ttu-id="ec35e-266">Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da [verbi HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="ec35e-266">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="ec35e-267">Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-267">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ec35e-268">Il `StartUp.Configure` codice seguente è tipico per un'API REST e viene usato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-268">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

<span data-ttu-id="ec35e-269">Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> viene chiamato all'interno `UseEndpoints` di per eseguire il mapping dei controller indirizzati degli attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-269">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="ec35e-270">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-270">In the following example:</span></span>

* <span data-ttu-id="ec35e-271">`Configure`Viene utilizzato il metodo precedente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-271">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="ec35e-272">`HomeController`corrisponde a un set di URL simile a quello che la route convenzionale predefinita `{controller=Home}/{action=Index}/{id?}` corrisponde.</span><span class="sxs-lookup"><span data-stu-id="ec35e-272">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="ec35e-273">L' `HomeController.Index` azione viene eseguita per uno dei percorsi URL `/` ,, `/Home` `/Home/Index` o `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-273">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="ec35e-274">In questo esempio viene evidenziata una differenza di programmazione principale tra routing degli attributi e [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="ec35e-274">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="ec35e-275">Il routing degli attributi richiede un maggior numero di input per specificare una route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-275">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="ec35e-276">La route predefinita convenzionale gestisce le route in maniera più concisa.</span><span class="sxs-lookup"><span data-stu-id="ec35e-276">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ec35e-277">Tuttavia, il routing degli attributi consente e richiede un controllo preciso dei modelli di route applicabili a ciascuna [azione](#action).</span><span class="sxs-lookup"><span data-stu-id="ec35e-277">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="ec35e-278">Nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-278">In the following code:</span></span>

* <span data-ttu-id="ec35e-279">Il nome del controller e i nomi delle azioni non rivestono **alcun** ruolo in cui viene confrontata l'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-279">The controller name and action names play **no** role in which action is matched.</span></span>
* <span data-ttu-id="ec35e-280">Corrisponde agli URL dell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-280">Matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ec35e-281">Il codice seguente usa la sostituzione dei token per `action` e `controller` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-281">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="ec35e-282">Il codice seguente si applica `[Route("[controller]/[action]")]` al controller:</span><span class="sxs-lookup"><span data-stu-id="ec35e-282">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ec35e-283">Nel codice precedente, i `Index` modelli di metodo devono anteporre `/` o `~/` ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-283">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="ec35e-284">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-284">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="ec35e-285">Vedere [precedenza del modello di route](xref:fundamentals/routing#rtp) per informazioni sulla selezione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-285">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="ec35e-286">Nomi riservati di routing</span><span class="sxs-lookup"><span data-stu-id="ec35e-286">Reserved routing names</span></span>

<span data-ttu-id="ec35e-287">Quando si usano i controller o le pagine, le parole chiave seguenti sono nomi di parametro di route riservati Razor :</span><span class="sxs-lookup"><span data-stu-id="ec35e-287">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="ec35e-288">`page`L'uso di come parametro di route con routing degli attributi è un errore comune.</span><span class="sxs-lookup"><span data-stu-id="ec35e-288">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="ec35e-289">Questa operazione comporta un comportamento incoerente e confuso con la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-289">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="ec35e-290">I nomi di parametro speciali vengono usati dalla generazione dell'URL per determinare se un'operazione di generazione URL fa riferimento a una Razor pagina o a un controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-290">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="ec35e-291">Modelli di verbi HTTP</span><span class="sxs-lookup"><span data-stu-id="ec35e-291">HTTP verb templates</span></span>

<span data-ttu-id="ec35e-292">ASP.NET Core dispone dei seguenti modelli di verbi HTTP:</span><span class="sxs-lookup"><span data-stu-id="ec35e-292">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="ec35e-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-293">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="ec35e-294">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-294">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="ec35e-295">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-295">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="ec35e-296">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-296">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="ec35e-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-297">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="ec35e-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-298">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="ec35e-299">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="ec35e-299">Route templates</span></span>

<span data-ttu-id="ec35e-300">ASP.NET Core include i modelli di route seguenti:</span><span class="sxs-lookup"><span data-stu-id="ec35e-300">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="ec35e-301">Tutti i [modelli di verbi HTTP](#verb) sono modelli di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-301">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="ec35e-302">[Route](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="ec35e-302">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="ec35e-303">Routing degli attributi con attributi di verbi http</span><span class="sxs-lookup"><span data-stu-id="ec35e-303">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="ec35e-304">Si consideri il controller seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-304">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="ec35e-305">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-305">In the preceding code:</span></span>

* <span data-ttu-id="ec35e-306">Ogni azione contiene l' `[HttpGet]` attributo, che vincola la corrispondenza solo alle richieste HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="ec35e-306">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="ec35e-307">L' `GetProduct` azione include il `"{id}"` modello, pertanto `id` viene aggiunto al `"api/[controller]"` modello sul controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-307">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="ec35e-308">Il modello di metodi è `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-308">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="ec35e-309">Pertanto questa azione corrisponde solo alle richieste Get di per il form,, e `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` così via.</span><span class="sxs-lookup"><span data-stu-id="ec35e-309">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="ec35e-310">L' `GetIntProduct` azione contiene il `"int/{id:int}")` modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-310">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="ec35e-311">La `:int` parte del modello vincola i `id` valori di route alle stringhe che possono essere convertite in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="ec35e-311">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="ec35e-312">Una richiesta GET a `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-312">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="ec35e-313">Non corrisponde a questa azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-313">Doesn't match this action.</span></span>
  * <span data-ttu-id="ec35e-314">Restituisce un errore [404 non trovato](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-314">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="ec35e-315">L' `GetInt2Product` azione contiene `{id}` nel modello, ma non vincola i `id` valori che possono essere convertiti in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="ec35e-315">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="ec35e-316">Una richiesta GET a `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-316">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="ec35e-317">Corrisponde a questa route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-317">Matches this route.</span></span>
  * <span data-ttu-id="ec35e-318">L'associazione di modelli non riesce a eseguire `abc` la conversione in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="ec35e-318">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="ec35e-319">Il `id` parametro del metodo è Integer.</span><span class="sxs-lookup"><span data-stu-id="ec35e-319">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="ec35e-320">Restituisce una [richiesta non valida 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) perché l'associazione di modelli non è riuscita a eseguire la conversione `abc` in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="ec35e-320">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="ec35e-321">Il routing degli attributi può utilizzare <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributi come <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="ec35e-321">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="ec35e-322">Tutti gli attributi del [verbo http](#verb) accettano un modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-322">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="ec35e-323">Nell'esempio seguente vengono illustrate due azioni che corrispondono allo stesso modello di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-323">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="ec35e-324">Utilizzando il percorso URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-324">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="ec35e-325">L' `MyProductsController.ListProducts` azione viene eseguita quando il [verbo http](#verb) è `GET` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-325">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="ec35e-326">L' `MyProductsController.CreateProduct` azione viene eseguita quando il [verbo http](#verb) è `POST` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-326">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="ec35e-327">Quando si compila un'API REST, è raro che sia necessario usare `[Route(...)]` su un metodo di azione perché l'azione accetta tutti i metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-327">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="ec35e-328">È preferibile usare l' [attributo verbo http](#verb) più specifico per essere precisi sul supporto dell'API.</span><span class="sxs-lookup"><span data-stu-id="ec35e-328">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="ec35e-329">I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.</span><span class="sxs-lookup"><span data-stu-id="ec35e-329">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ec35e-330">Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da verbi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-330">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="ec35e-331">Ciò significa che molte operazioni, ad esempio GET e POST nella stessa risorsa logica, utilizzano lo stesso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-331">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="ec35e-332">Il routing degli attributi offre un livello di controllo necessario per progettare con attenzione il layout dell'endpoint pubblico di un'API.</span><span class="sxs-lookup"><span data-stu-id="ec35e-332">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="ec35e-333">Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-333">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ec35e-334">Nell'esempio seguente `id` è necessario come parte del percorso URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-334">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ec35e-335">`Products2ApiController.GetProduct(int)`Azione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-335">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="ec35e-336">Viene eseguito con un percorso URL come`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="ec35e-336">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="ec35e-337">Non viene eseguito con il percorso URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-337">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="ec35e-338">L'attributo [[consums]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) consente a un'azione di limitare i tipi di contenuto della richiesta supportati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-338">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="ec35e-339">Per altre informazioni, vedere [definire i tipi di contenuto della richiesta supportati con l'attributo consums](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="ec35e-339">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="ec35e-340">Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.</span><span class="sxs-lookup"><span data-stu-id="ec35e-340">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="ec35e-341">Per altre informazioni su `[ApiController]` , vedere [attributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="ec35e-341">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="ec35e-342">Nome route</span><span class="sxs-lookup"><span data-stu-id="ec35e-342">Route name</span></span>

<span data-ttu-id="ec35e-343">Nell’esempio di codice riportato di seguito viene definito un nome di route di `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-343">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="ec35e-344">I nomi di route possono essere usati per generare un URL in base a un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="ec35e-344">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ec35e-345">Nomi di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-345">Route names:</span></span>

* <span data-ttu-id="ec35e-346">Non hanno alcun effetto sul comportamento di corrispondenza dell'URL del routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-346">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="ec35e-347">Vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-347">Are only used for URL generation.</span></span>

<span data-ttu-id="ec35e-348">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-348">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ec35e-349">Confrontare il codice precedente con la route predefinita convenzionale, che definisce il `id` parametro come facoltativo ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="ec35e-349">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ec35e-350">La possibilità di specificare con precisione le API offre vantaggi, ad esempio consentendo `/products` e `/products/5` di essere inviati a diverse azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-350">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="ec35e-351">Combinazione di route per attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-351">Combining attribute routes</span></span>

<span data-ttu-id="ec35e-352">Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-352">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ec35e-353">I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-353">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ec35e-354">Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-354">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="ec35e-355">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-355">In the preceding example:</span></span>

* <span data-ttu-id="ec35e-356">Il percorso URL `/products` può corrispondere`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="ec35e-356">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="ec35e-357">Il percorso URL `/products/5` può corrispondere `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-357">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="ec35e-358">Entrambe le azioni corrispondono solo a HTTP `GET` perché sono contrassegnate con l' `[HttpGet]` attributo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-358">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="ec35e-359">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-359">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ec35e-360">Nell'esempio seguente viene individuata la corrispondenza di un set di percorsi URL simile a quello della route predefinita.</span><span class="sxs-lookup"><span data-stu-id="ec35e-360">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="ec35e-361">Nella tabella seguente vengono illustrati gli `[Route]` attributi del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-361">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="ec35e-362">Attributo</span><span class="sxs-lookup"><span data-stu-id="ec35e-362">Attribute</span></span>               | <span data-ttu-id="ec35e-363">Combina con`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="ec35e-363">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="ec35e-364">Definisce il modello di route</span><span class="sxs-lookup"><span data-stu-id="ec35e-364">Defines route template</span></span> |
| ---
<span data-ttu-id="ec35e-365">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-365">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-366">'Blazor'</span></span>
- <span data-ttu-id="ec35e-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-367">'Identity'</span></span>
- <span data-ttu-id="ec35e-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-369">'Razor'</span></span>
- <span data-ttu-id="ec35e-370">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-371">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-371">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-372">'Blazor'</span></span>
- <span data-ttu-id="ec35e-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-373">'Identity'</span></span>
- <span data-ttu-id="ec35e-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-375">'Razor'</span></span>
- <span data-ttu-id="ec35e-376">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-377">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-377">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-378">'Blazor'</span></span>
- <span data-ttu-id="ec35e-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-379">'Identity'</span></span>
- <span data-ttu-id="ec35e-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-381">'Razor'</span></span>
- <span data-ttu-id="ec35e-382">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-383">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-383">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-384">'Blazor'</span></span>
- <span data-ttu-id="ec35e-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-385">'Identity'</span></span>
- <span data-ttu-id="ec35e-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-387">'Razor'</span></span>
- <span data-ttu-id="ec35e-388">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-389">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-389">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-390">'Blazor'</span></span>
- <span data-ttu-id="ec35e-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-391">'Identity'</span></span>
- <span data-ttu-id="ec35e-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-393">'Razor'</span></span>
- <span data-ttu-id="ec35e-394">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-395">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-395">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-396">'Blazor'</span></span>
- <span data-ttu-id="ec35e-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-397">'Identity'</span></span>
- <span data-ttu-id="ec35e-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-399">'Razor'</span></span>
- <span data-ttu-id="ec35e-400">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-400">'SignalR' uid:</span></span> 

<span data-ttu-id="ec35e-401">--------- | Titolo---: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-401">--------- | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-402">'Blazor'</span></span>
- <span data-ttu-id="ec35e-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-403">'Identity'</span></span>
- <span data-ttu-id="ec35e-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-405">'Razor'</span></span>
- <span data-ttu-id="ec35e-406">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-407">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-407">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-408">'Blazor'</span></span>
- <span data-ttu-id="ec35e-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-409">'Identity'</span></span>
- <span data-ttu-id="ec35e-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-411">'Razor'</span></span>
- <span data-ttu-id="ec35e-412">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-413">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-413">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-414">'Blazor'</span></span>
- <span data-ttu-id="ec35e-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-415">'Identity'</span></span>
- <span data-ttu-id="ec35e-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-417">'Razor'</span></span>
- <span data-ttu-id="ec35e-418">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-419">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-419">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-420">'Blazor'</span></span>
- <span data-ttu-id="ec35e-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-421">'Identity'</span></span>
- <span data-ttu-id="ec35e-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-423">'Razor'</span></span>
- <span data-ttu-id="ec35e-424">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-424">'SignalR' uid:</span></span> 

<span data-ttu-id="ec35e-425">------ | Titolo---: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-425">------ | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-426">'Blazor'</span></span>
- <span data-ttu-id="ec35e-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-427">'Identity'</span></span>
- <span data-ttu-id="ec35e-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-429">'Razor'</span></span>
- <span data-ttu-id="ec35e-430">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ec35e-431">title: autore: Descrizione: ms. Author: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ec35e-431">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="ec35e-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-432">'Blazor'</span></span>
- <span data-ttu-id="ec35e-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ec35e-433">'Identity'</span></span>
- <span data-ttu-id="ec35e-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ec35e-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="ec35e-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ec35e-435">'Razor'</span></span>
- <span data-ttu-id="ec35e-436">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="ec35e-436">'SignalR' uid:</span></span> 

<span data-ttu-id="ec35e-437">----- | | `[Route("")]` | Sì | `"Home"` |
| `[Route("Index")]` | Sì | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
 | `[Route("About")]` | Sì | `"Home/About"`|</span><span class="sxs-lookup"><span data-stu-id="ec35e-437">----- | | `[Route("")]` | Yes | `"Home"` |
| `[Route("Index")]` | Yes | `"Home/Index"` |
| `[Route("/")]` | **No** | `""` |
| `[Route("About")]` | Yes | `"Home/About"` |</span></span>

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="ec35e-438">Ordine Route attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-438">Attribute route order</span></span>

<span data-ttu-id="ec35e-439">Il routing compila un albero e corrisponde a tutti gli endpoint contemporaneamente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-439">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="ec35e-440">Le voci della route si comportano come se fossero posizionate in un ordinamento ideale.</span><span class="sxs-lookup"><span data-stu-id="ec35e-440">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="ec35e-441">Le route più specifiche hanno la possibilità di essere eseguite prima delle route più generali.</span><span class="sxs-lookup"><span data-stu-id="ec35e-441">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ec35e-442">Ad esempio, una route di attributo come `blog/search/{topic}` è più specifica di una route di attributo come `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-442">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="ec35e-443">`blog/search/{topic}`Per impostazione predefinita, la route ha una priorità più elevata perché è più specifica.</span><span class="sxs-lookup"><span data-stu-id="ec35e-443">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="ec35e-444">Utilizzando il [routing convenzionale](#cr), lo sviluppatore è responsabile dell'inserimento delle route nell'ordine desiderato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-444">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ec35e-445">Le route degli attributi possono configurare un ordine utilizzando la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ec35e-445">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="ec35e-446">Tutti gli [attributi di route](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) forniti dal Framework includono `Order` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-446">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="ec35e-447">Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-447">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ec35e-448">L'ordine predefinito è `0`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-448">The default order is `0`.</span></span> <span data-ttu-id="ec35e-449">L'impostazione di una route tramite `Order = -1` viene eseguita prima delle route che non impostano un ordine.</span><span class="sxs-lookup"><span data-stu-id="ec35e-449">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="ec35e-450">L'impostazione di una route tramite `Order = 1` viene eseguita dopo l'ordinamento predefinito della route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-450">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="ec35e-451">**Evitare** la dipendenza da `Order` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-451">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="ec35e-452">Se lo spazio URL di un'app richiede che i valori di ordine espliciti vengano indirizzati correttamente, è probabile che anche i client siano confusi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-452">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ec35e-453">In generale, il routing degli attributi seleziona la route corretta con l'URL corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-453">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="ec35e-454">Se l'ordine predefinito usato per la generazione di URL non funziona, l'uso di un nome di route come override è in genere più semplice dell'applicazione della `Order` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ec35e-454">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="ec35e-455">Considerare i seguenti due controller che definiscono entrambi la corrispondenza della route `/home` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-455">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="ec35e-456">La richiesta `/home` con il codice precedente genera un'eccezione simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-456">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="ec35e-457">`Order`L'aggiunta a uno degli attributi di route risolve l'ambiguità:</span><span class="sxs-lookup"><span data-stu-id="ec35e-457">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="ec35e-458">Con il codice precedente, `/home` esegue l' `HomeController.Index` endpoint.</span><span class="sxs-lookup"><span data-stu-id="ec35e-458">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="ec35e-459">Per ottenere l'oggetto `MyDemoController.MyIndex` , richiedere `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-459">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="ec35e-460">**Nota**:</span><span class="sxs-lookup"><span data-stu-id="ec35e-460">**Note**:</span></span>

* <span data-ttu-id="ec35e-461">Il codice precedente è un esempio o una progettazione di routing insufficiente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-461">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="ec35e-462">È stato usato per illustrare la `Order` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="ec35e-462">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="ec35e-463">La `Order` Proprietà risolve solo l'ambiguità e non è possibile trovare una corrispondenza per il modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-463">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="ec35e-464">È preferibile rimuovere il `[Route("Home")]` modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-464">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="ec35e-465">Vedere la pagina relativa alle [ Razor convenzioni di route e app: ordine](xref:razor-pages/razor-pages-conventions#route-order) di route per informazioni sull'ordine di route con Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="ec35e-465">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="ec35e-466">In alcuni casi, viene restituito un errore HTTP 500 con route ambigue.</span><span class="sxs-lookup"><span data-stu-id="ec35e-466">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="ec35e-467">Usare la [registrazione](xref:fundamentals/logging/index) per verificare quali endpoint hanno causato `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-467">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ec35e-468">Sostituzione di token nei modelli di route [controller], [azione], [area]</span><span class="sxs-lookup"><span data-stu-id="ec35e-468">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="ec35e-469">Per praticità, le route degli attributi supportano la sostituzione dei token per i parametri di route riservati, racchiudendo un token in uno dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="ec35e-469">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="ec35e-470">Parentesi quadre:`[]`</span><span class="sxs-lookup"><span data-stu-id="ec35e-470">Square brackets: `[]`</span></span>
* <span data-ttu-id="ec35e-471">Parentesi graffe:`{}`</span><span class="sxs-lookup"><span data-stu-id="ec35e-471">Curly braces: `{}`</span></span>

<span data-ttu-id="ec35e-472">I token `[action]` , `[area]` e `[controller]` vengono sostituiti con i valori del nome dell'azione, del nome dell'area e del nome del controller dall'azione in cui è definita la route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-472">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ec35e-473">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-473">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="ec35e-474">Corrisponde`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="ec35e-474">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="ec35e-475">Corrisponde`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="ec35e-475">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="ec35e-476">La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-476">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ec35e-477">L'esempio precedente si comporta come il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-477">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="ec35e-478">Le route con attributi possono anche essere combinate con l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="ec35e-478">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ec35e-479">Questa funzionalità è potente in combinazione con la sostituzione dei token.</span><span class="sxs-lookup"><span data-stu-id="ec35e-479">This is powerful combined with token replacement.</span></span> <span data-ttu-id="ec35e-480">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-480">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="ec35e-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nome di route univoco per ogni azione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-481">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="ec35e-482">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-482">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="ec35e-483"> genera un nome di route univoco per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-483">generates a unique route name for each action.</span></span>

<span data-ttu-id="ec35e-484">Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).</span><span class="sxs-lookup"><span data-stu-id="ec35e-484">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ec35e-485">Usare un trasformatore di parametri per personalizzare la sostituzione dei token</span><span class="sxs-lookup"><span data-stu-id="ec35e-485">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ec35e-486">La sostituzione dei token può essere personalizzata usando un trasformatore di parametri.</span><span class="sxs-lookup"><span data-stu-id="ec35e-486">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ec35e-487">Un trasformatore di parametri implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e trasforma il valore dei parametri.</span><span class="sxs-lookup"><span data-stu-id="ec35e-487">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="ec35e-488">Un trasformatore di parametro personalizzato, ad esempio, `SlugifyParameterTransformer` modifica il `SubscriptionManagement` valore di route in `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-488">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="ec35e-489"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> è una convenzione del modello di applicazione che:</span><span class="sxs-lookup"><span data-stu-id="ec35e-489">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="ec35e-490">Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-490">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ec35e-491">Personalizza i valori dei token delle route di attributi quando vengono sostituiti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-491">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="ec35e-492">Il `ListAll` metodo precedente corrisponde a `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-492">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="ec35e-493">La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-493">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="ec35e-494">Per la definizione di Slug, vedere la [documentazione Web MDN in slug](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-494">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="ec35e-495">Route di più attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-495">Multiple attribute routes</span></span>

<span data-ttu-id="ec35e-496">Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-496">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ec35e-497">L'uso più comune è simulare il comportamento della route convenzionale predefinita come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-497">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="ec35e-498">L'inserimento di più attributi di route sul controller significa che ognuno di essi si combina con ognuno degli attributi di route nei metodi di azione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-498">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="ec35e-499">Tutti i vincoli di route [http verb](#verb) implementano `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-499">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="ec35e-500">Quando più attributi di route che implementano <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sono posizionati in un'azione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-500">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="ec35e-501">Ogni vincolo di azione viene combinato con il modello di route applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-501">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="ec35e-502">L'uso di più route sulle azioni potrebbe sembrare utile e potente, ma è preferibile tenere lo spazio URL dell'app di base e ben definito.</span><span class="sxs-lookup"><span data-stu-id="ec35e-502">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="ec35e-503">Usare più route sulle azioni **solo** se necessario, ad esempio per supportare i client esistenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-503">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ec35e-504">Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-504">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ec35e-505">Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.</span><span class="sxs-lookup"><span data-stu-id="ec35e-505">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="ec35e-506">Nel codice precedente, `[HttpPost("product/{id:int}")]` applica un vincolo di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-506">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="ec35e-507">L' `ProductsController.ShowProduct` azione corrisponde solo ai percorsi URL, ad esempio `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-507">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="ec35e-508">La parte del modello `{id:int}` di route vincola tale segmento ai soli numeri interi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-508">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="ec35e-509">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-509">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ec35e-510">Attributi di Route personalizzati con IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="ec35e-510">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="ec35e-511">Tutti gli [attributi di route](#rt) implementano <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="ec35e-511">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="ec35e-512">Il runtime di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ec35e-512">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="ec35e-513">Cerca gli attributi nelle classi controller e nei metodi di azione all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="ec35e-513">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="ec35e-514">USA gli attributi che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-514">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ec35e-515">Implementare `IRouteTemplateProvider` per definire gli attributi di Route personalizzati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-515">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="ec35e-516">Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:</span><span class="sxs-lookup"><span data-stu-id="ec35e-516">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="ec35e-517">Il `Get` metodo precedente restituisce `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-517">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ec35e-518">Usare il modello di applicazione per personalizzare le route degli attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-518">Use application model to customize attribute routes</span></span>

<span data-ttu-id="ec35e-519">Modello di applicazione:</span><span class="sxs-lookup"><span data-stu-id="ec35e-519">The application model:</span></span>

* <span data-ttu-id="ec35e-520">È un modello a oggetti creato all'avvio.</span><span class="sxs-lookup"><span data-stu-id="ec35e-520">Is an object model created at startup.</span></span>
* <span data-ttu-id="ec35e-521">Contiene tutti i metadati usati da ASP.NET Core per indirizzare ed eseguire le azioni in un'app.</span><span class="sxs-lookup"><span data-stu-id="ec35e-521">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="ec35e-522">Il modello di applicazione include tutti i dati raccolti dagli attributi di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-522">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="ec35e-523">I dati degli attributi della route vengono forniti dall' `IRouteTemplateProvider` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="ec35e-523">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="ec35e-524">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="ec35e-524">Conventions:</span></span>

* <span data-ttu-id="ec35e-525">È possibile scrivere per modificare il modello di applicazione per personalizzare il comportamento del routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-525">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="ec35e-526">Vengono letti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="ec35e-526">Are read at app startup.</span></span>

<span data-ttu-id="ec35e-527">Questa sezione illustra un esempio di base di personalizzazione del routing con il modello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-527">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="ec35e-528">Il codice seguente rende le route approssimativamente allineate con la struttura di cartelle del progetto.</span><span class="sxs-lookup"><span data-stu-id="ec35e-528">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="ec35e-529">Il codice seguente impedisce `namespace` che la Convenzione venga applicata ai controller indirizzati con l'attributo:</span><span class="sxs-lookup"><span data-stu-id="ec35e-529">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="ec35e-530">Ad esempio, il controller seguente non usa `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-530">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="ec35e-531">Il metodo `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-531">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="ec35e-532">Non esegue alcuna operazione se il controller è instradato all'attributo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-532">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="ec35e-533">Imposta il modello di controller basato su `namespace` , con la base `namespace` rimossa.</span><span class="sxs-lookup"><span data-stu-id="ec35e-533">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="ec35e-534">Il `NamespaceRoutingConvention` può essere applicato in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-534">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="ec35e-535">Si consideri, ad esempio, il controller seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-535">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="ec35e-536">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-536">In the preceding code:</span></span>

* <span data-ttu-id="ec35e-537">La base `namespace` è `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-537">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="ec35e-538">Il nome completo del controller precedente è `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-538">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="ec35e-539">`NamespaceRoutingConvention`Imposta il modello dei controller su `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-539">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="ec35e-540">Il `NamespaceRoutingConvention` può essere applicato anche come attributo in un controller:</span><span class="sxs-lookup"><span data-stu-id="ec35e-540">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ec35e-541">Routing misto: routing con attributi e routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-541">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ec35e-542">ASP.NET Core app possono combinare l'uso del routing convenzionale e del routing degli attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-542">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ec35e-543">In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.</span><span class="sxs-lookup"><span data-stu-id="ec35e-543">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ec35e-544">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-544">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ec35e-545">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-545">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ec35e-546">Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa.</span><span class="sxs-lookup"><span data-stu-id="ec35e-546">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ec35e-547">**Qualsiasi** attributo di route nel controller esegue il routing di **tutte** le azioni nell'attributo controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-547">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="ec35e-548">Il routing degli attributi e il routing convenzionale utilizzano lo stesso motore di routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-548">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="ec35e-549">Generazione di URL e valori di ambiente</span><span class="sxs-lookup"><span data-stu-id="ec35e-549">URL Generation and ambient values</span></span>

<span data-ttu-id="ec35e-550">Le app possono usare le funzionalità di generazione URL di routing per generare collegamenti URL alle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-550">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ec35e-551">La generazione di URL Elimina gli URL hardcoded, rendendo il codice più affidabile e gestibile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-551">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="ec35e-552">Questa sezione è incentrata sulle funzionalità di generazione degli URL fornite da MVC e copre solo le nozioni di base sul funzionamento della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-552">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="ec35e-553">Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-553">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ec35e-554">L' <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaccia è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-554">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ec35e-555">Un'istanza di `IUrlHelper` è disponibile tramite la `Url` Proprietà in controller, visualizzazioni e componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-555">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ec35e-556">Nell'esempio seguente l' `IUrlHelper` interfaccia viene utilizzata tramite la `Controller.Url` proprietà per generare un URL a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-556">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ec35e-557">Se l'app usa la route convenzionale predefinita, il valore della `url` variabile è la stringa del percorso dell'URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-557">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ec35e-558">Questo percorso URL viene creato tramite il routing combinando:</span><span class="sxs-lookup"><span data-stu-id="ec35e-558">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="ec35e-559">Valori della route dalla richiesta corrente, denominati **valori di ambiente**.</span><span class="sxs-lookup"><span data-stu-id="ec35e-559">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="ec35e-560">I valori passati a `Url.Action` e sostituiscono tali valori nel modello di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-560">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ec35e-561">Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-561">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ec35e-562">Un parametro di route che non ha un valore può:</span><span class="sxs-lookup"><span data-stu-id="ec35e-562">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="ec35e-563">Utilizzare un valore predefinito se ne è presente uno.</span><span class="sxs-lookup"><span data-stu-id="ec35e-563">Use a default value if it has one.</span></span>
* <span data-ttu-id="ec35e-564">Viene ignorato se è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-564">Be skipped if it's optional.</span></span> <span data-ttu-id="ec35e-565">Ad esempio, `id` dal modello di route `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-565">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="ec35e-566">La generazione di URL non riesce se un parametro di route obbligatorio non ha un valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-566">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ec35e-567">Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-567">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ec35e-568">Nell'esempio precedente di viene `Url.Action` presupposto il [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="ec35e-568">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="ec35e-569">La generazione di URL funziona in modo analogo al [routing degli attributi](#ar), sebbene i concetti siano diversi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-569">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="ec35e-570">Con routing convenzionale:</span><span class="sxs-lookup"><span data-stu-id="ec35e-570">With conventional routing:</span></span>

* <span data-ttu-id="ec35e-571">I valori di route vengono usati per espandere un modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-571">The route values are used to expand a template.</span></span>
* <span data-ttu-id="ec35e-572">I valori di route per `controller` e `action` vengono in genere visualizzati in tale modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-572">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="ec35e-573">Questa operazione funziona perché gli URL corrispondenti al routing rispettano una convenzione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-573">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="ec35e-574">Nell'esempio seguente viene usato il routing degli attributi:</span><span class="sxs-lookup"><span data-stu-id="ec35e-574">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="ec35e-575">L' `Source` azione nel codice precedente genera `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-575">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="ec35e-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>è stato aggiunto in ASP.NET Core 3,0 come alternativa a `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-576"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="ec35e-577">`LinkGenerator`offre funzionalità simili ma più flessibili.</span><span class="sxs-lookup"><span data-stu-id="ec35e-577">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="ec35e-578">Ogni metodo in `IUrlHelper` dispone anche di una famiglia di metodi corrispondente `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-578">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ec35e-579">Generazione di URL in base al nome dell'azione</span><span class="sxs-lookup"><span data-stu-id="ec35e-579">Generating URLs by action name</span></span>

<span data-ttu-id="ec35e-580">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e tutti gli overload correlati sono progettati per generare l'endpoint di destinazione specificando il nome del controller e il nome dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-580">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="ec35e-581">Quando `Url.Action` si usa, i valori di route correnti per `controller` e `action` vengono forniti dal runtime:</span><span class="sxs-lookup"><span data-stu-id="ec35e-581">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="ec35e-582">Il valore di `controller` e `action` fa parte dei [valori di ambiente](#ambient) e.</span><span class="sxs-lookup"><span data-stu-id="ec35e-582">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="ec35e-583">Il metodo `Url.Action` Usa sempre i valori correnti di `action` e `controller` e genera un percorso URL che indirizza all'azione corrente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-583">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="ec35e-584">Il routing tenta di usare i valori nei valori di ambiente per inserire le informazioni non fornite durante la generazione di un URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-584">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="ec35e-585">Si consideri una route come `{a}/{b}/{c}/{d}` con i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-585">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="ec35e-586">Il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-586">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="ec35e-587">Il routing ha informazioni sufficienti perché tutti i parametri di route hanno un valore.</span><span class="sxs-lookup"><span data-stu-id="ec35e-587">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="ec35e-588">Se il valore `{ d = Donovan }` viene aggiunto:</span><span class="sxs-lookup"><span data-stu-id="ec35e-588">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="ec35e-589">Il valore `{ d = David }` viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-589">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="ec35e-590">Il percorso dell'URL generato è `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-590">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="ec35e-591">**Avviso**: i percorsi URL sono gerarchici.</span><span class="sxs-lookup"><span data-stu-id="ec35e-591">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="ec35e-592">Nell'esempio precedente, se il valore `{ c = Cheryl }` viene aggiunto:</span><span class="sxs-lookup"><span data-stu-id="ec35e-592">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="ec35e-593">Entrambi i valori `{ c = Carol, d = David }` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-593">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="ec35e-594">Non esiste più un valore per `d` e la generazione dell'URL ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-594">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="ec35e-595">`c` `d` Per generare un URL, è necessario specificare i valori desiderati di e.</span><span class="sxs-lookup"><span data-stu-id="ec35e-595">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="ec35e-596">È possibile che si verifichi questo problema con la route predefinita `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-596">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="ec35e-597">Questo problema è raro in pratica perché `Url.Action` specifica sempre in modo esplicito un `controller` `action` valore e.</span><span class="sxs-lookup"><span data-stu-id="ec35e-597">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="ec35e-598">Diversi overload di [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) accettano un oggetto dei valori di route per fornire valori per i parametri di route diversi da `controller` e `action` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-598">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ec35e-599">L'oggetto valori di route viene spesso usato con `id` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-599">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="ec35e-600">Ad esempio: `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-600">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ec35e-601">Oggetto valori di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-601">The route values object:</span></span>

* <span data-ttu-id="ec35e-602">Per convenzione è in genere un oggetto di tipo anonimo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-602">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="ec35e-603">Può essere un oggetto `IDictionary<>` o un oggetto [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="ec35e-603">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="ec35e-604">I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="ec35e-604">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="ec35e-605">Il codice precedente genera `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-605">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="ec35e-606">Il codice seguente genera un URL assoluto:</span><span class="sxs-lookup"><span data-stu-id="ec35e-606">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="ec35e-607">Per creare un URL assoluto, usare uno dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="ec35e-607">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="ec35e-608">Overload che accetta un oggetto `protocol` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-608">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="ec35e-609">Ad esempio, il codice precedente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-609">For example, the preceding code.</span></span>
* <span data-ttu-id="ec35e-610">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), che genera URI assoluti per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="ec35e-610">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="ec35e-611">Genera URL per Route</span><span class="sxs-lookup"><span data-stu-id="ec35e-611">Generate URLs by route</span></span>

<span data-ttu-id="ec35e-612">Nel codice precedente è stata illustrata la generazione di un URL passando il nome del controller e dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-612">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ec35e-613">`IUrlHelper`fornisce anche la famiglia di metodi [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-613">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="ec35e-614">Questi metodi sono simili a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ma non copiano i valori correnti di `action` e `controller` nei valori di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-614">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ec35e-615">L'utilizzo più comune di `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-615">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="ec35e-616">Specifica un nome di route per generare l'URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-616">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="ec35e-617">In genere non specifica un nome di controller o di azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-617">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="ec35e-618">Il Razor file seguente genera un collegamento HTML a `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-618">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="ec35e-619">Genera URL in HTML eRazor</span><span class="sxs-lookup"><span data-stu-id="ec35e-619">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="ec35e-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornisce i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metodi [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) per generare `<form>` `<a>` rispettivamente gli elementi e.</span><span class="sxs-lookup"><span data-stu-id="ec35e-620"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ec35e-621">Questi metodi usano il metodo [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) per generare un URL e accettano argomenti simili.</span><span class="sxs-lookup"><span data-stu-id="ec35e-621">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ec35e-622">Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-622">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ec35e-623">Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-623">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ec35e-624">Entrambi usano `IUrlHelper` per la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-624">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ec35e-625">Per ulteriori informazioni, vedere [Helper tag nei moduli](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-625">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="ec35e-626">All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-626">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="ec35e-627">Generazione dell'URL nei risultati dell'azione</span><span class="sxs-lookup"><span data-stu-id="ec35e-627">URL generation in Action Results</span></span>

<span data-ttu-id="ec35e-628">Negli esempi precedenti è stato illustrato l'utilizzo `IUrlHelper` di in un controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-628">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="ec35e-629">L'utilizzo più comune in un controller consiste nel generare un URL come parte del risultato di un'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-629">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ec35e-630">Le classi di base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-630">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ec35e-631">Un utilizzo tipico prevede il reindirizzamento dopo l'accettazione dell'input utente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-631">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="ec35e-632">I metodi factory dei risultati delle azioni come <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> seguono un modello simile ai metodi in `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-632">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ec35e-633">Caso speciale per le route convenzionali dedicate</span><span class="sxs-lookup"><span data-stu-id="ec35e-633">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ec35e-634">Il [routing convenzionale](#cr) può usare un tipo speciale di definizione di route denominata [Route convenzionale dedicata](#dcr).</span><span class="sxs-lookup"><span data-stu-id="ec35e-634">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="ec35e-635">Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata:</span><span class="sxs-lookup"><span data-stu-id="ec35e-635">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="ec35e-636">Usando le definizioni di route precedenti, `Url.Action("Index", "Home")` genera il percorso URL `/` usando la `default` Route, ma perché?</span><span class="sxs-lookup"><span data-stu-id="ec35e-636">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="ec35e-637">Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-637">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ec35e-638">Le [Route convenzionali dedicate](#dcr) si basano su un comportamento speciale dei valori predefiniti che non hanno un parametro di route corrispondente che impedisce che la route sia troppo [greedy](xref:fundamentals/routing#greedy) con la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-638">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="ec35e-639">In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-639">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ec35e-640">Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-640">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ec35e-641">La generazione di URL con ha `blog` esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-641">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ec35e-642">Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-642">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ec35e-643">Aree</span><span class="sxs-lookup"><span data-stu-id="ec35e-643">Areas</span></span>

<span data-ttu-id="ec35e-644">Le [aree](xref:mvc/controllers/areas) sono una funzionalità di MVC utilizzata per organizzare le funzionalità correlate in un gruppo come separato:</span><span class="sxs-lookup"><span data-stu-id="ec35e-644">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="ec35e-645">Spazio dei nomi di routing per le azioni del controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-645">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="ec35e-646">Struttura di cartelle per le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-646">Folder structure for views.</span></span>

<span data-ttu-id="ec35e-647">L'uso delle aree consente a un'app di avere più controller con lo stesso nome, purché abbiano aree diverse.</span><span class="sxs-lookup"><span data-stu-id="ec35e-647">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="ec35e-648">Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-648">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ec35e-649">Questa sezione illustra il modo in cui il routing interagisce con le aree.</span><span class="sxs-lookup"><span data-stu-id="ec35e-649">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="ec35e-650">Per informazioni dettagliate sull'uso delle aree con le visualizzazioni, vedere [aree](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="ec35e-650">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="ec35e-651">Nell'esempio seguente viene configurato MVC per l'utilizzo della route convenzionale predefinita e di una `area` Route per un oggetto `area` denominato `Blog` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-651">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ec35e-652">Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> viene chiamato per creare l'oggetto `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-652">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="ec35e-653">Il secondo parametro, `"Blog"` , è il nome dell'area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-653">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="ec35e-654">Quando si corrisponde a un percorso URL come `/Manage/Users/AddUser` , la `"blog_route"` Route genera i valori della route `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-654">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ec35e-655">Il `area` valore di route viene prodotto da un valore predefinito per `area` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-655">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="ec35e-656">La route creata da `MapAreaControllerRoute` equivale a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="ec35e-656">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="ec35e-657">`MapAreaControllerRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-657">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ec35e-658">Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-658">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="ec35e-659">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-659">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ec35e-660">In generale, le route con aree devono essere posizionate in precedenza perché sono più specifiche delle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-660">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ec35e-661">Utilizzando l'esempio precedente, i valori della route `{ area = Blog, controller = Users, action = AddUser }` corrispondono all'azione seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-661">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ec35e-662">L'attributo [[area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) è quello che denota un controller come parte di un'area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-662">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="ec35e-663">Questo controller si trova nell' `Blog` area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-663">This controller is in the `Blog` area.</span></span> <span data-ttu-id="ec35e-664">I controller senza `[Area]` attributo non sono membri di nessuna area e non corrispondono **not** quando il `area` valore di route viene fornito dal routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-664">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ec35e-665">Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-665">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="ec35e-666">Lo spazio dei nomi di ogni controller viene visualizzato qui per completezza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-666">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="ec35e-667">Se il controller precedente usa lo stesso spazio dei nomi, verrà generato un errore del compilatore.</span><span class="sxs-lookup"><span data-stu-id="ec35e-667">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="ec35e-668">Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.</span><span class="sxs-lookup"><span data-stu-id="ec35e-668">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ec35e-669">I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-669">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ec35e-670">Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-670">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="ec35e-671">In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="ec35e-671">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ec35e-672">Quando si esegue un'azione all'interno di un'area, il valore di route per `area` è disponibile come [valore di ambiente](#ambient) per il routing da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-672">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="ec35e-673">Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-673">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="ec35e-674">Il codice seguente genera un URL per `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="ec35e-674">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="ec35e-675">Definizione di azione</span><span class="sxs-lookup"><span data-stu-id="ec35e-675">Action definition</span></span>

<span data-ttu-id="ec35e-676">I metodi pubblici in un controller, ad eccezione di quelli con l'attributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , sono azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-676">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ec35e-677">Codice di esempio</span><span class="sxs-lookup"><span data-stu-id="ec35e-677">Sample code</span></span>

 * <span data-ttu-id="ec35e-678">Il metodo [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) è incluso nel [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e viene usato per visualizzare le informazioni di routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-678">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="ec35e-679">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ec35e-679">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec35e-680">ASP.NET Core MVC usa [middleware](xref:fundamentals/middleware/index) di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-680">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="ec35e-681">Le route sono definite nel codice di avvio o negli attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-681">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="ec35e-682">Le route descrivono il modo in cui i percorsi URL devono corrispondere alle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-682">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="ec35e-683">Vengono usate anche per generare gli URL (per i collegamenti) inviati nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="ec35e-683">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="ec35e-684">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-684">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ec35e-685">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-685">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ec35e-686">Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="ec35e-686">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="ec35e-687">Questo documento illustra le interazioni tra MVC e il routing e il modo in cui le tipiche app MVC usano le funzionalità di routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-687">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="ec35e-688">Vedere [Routing](xref:fundamentals/routing) per informazioni dettagliate sul routing avanzato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-688">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="ec35e-689">Impostazione del middleware di routing</span><span class="sxs-lookup"><span data-stu-id="ec35e-689">Setting up Routing Middleware</span></span>

<span data-ttu-id="ec35e-690">Nel metodo *Configure* può apparire codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-690">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ec35e-691">Nella chiamata a `UseMvc`, si usa `MapRoute` per creare una singola route, a cui si farà riferimento come alla route predefinita (`default`).</span><span class="sxs-lookup"><span data-stu-id="ec35e-691">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="ec35e-692">La maggior parte delle app MVC userà una route con un modello simile alla route `default`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-692">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="ec35e-693">Il modello di route `"{controller=Home}/{action=Index}/{id?}"` può corrispondere a un percorso URL come `/Products/Details/5` ed estrae i valori di route `{ controller = Products, action = Details, id = 5 }` suddividendo il percorso in token.</span><span class="sxs-lookup"><span data-stu-id="ec35e-693">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="ec35e-694">MVC tenterà di individuare un controller denominato `ProductsController` e di eseguire l'azione `Details`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-694">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="ec35e-695">Si noti che in questo esempio l'associazione di modelli usa il valore di `id = 5` per impostare il parametro `id` su `5` quando si richiama l'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-695">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="ec35e-696">Per maggiori dettagli, vedere [Associazione di modelli](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="ec35e-696">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="ec35e-697">Usando la route `default`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-697">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ec35e-698">Il modello di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-698">The route template:</span></span>

* <span data-ttu-id="ec35e-699">`{controller=Home}` definisce `Home` come oggetto `controller` predefinito</span><span class="sxs-lookup"><span data-stu-id="ec35e-699">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="ec35e-700">`{action=Index}` definisce `Index` come oggetto `action` predefinito</span><span class="sxs-lookup"><span data-stu-id="ec35e-700">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="ec35e-701">`{id?}` definisce `id` come facoltativo</span><span class="sxs-lookup"><span data-stu-id="ec35e-701">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="ec35e-702">I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-702">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="ec35e-703">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-703">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="ec35e-704">`"{controller=Home}/{action=Index}/{id?}"` può corrispondere al percorso URL `/` e restituirà i valori di route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-704">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="ec35e-705">I valori per `controller` e `action` usano i valori predefiniti, `id` non genera un valore perché non esiste un segmento corrispondente nel percorso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-705">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="ec35e-706">MVC usa questi valori di route per selezionare l'azione `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-706">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="ec35e-707">Usando questa definizione del controller e il modello di route, l'azione `HomeController.Index` viene eseguita per tutti i percorsi URL seguenti:</span><span class="sxs-lookup"><span data-stu-id="ec35e-707">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="ec35e-708">Il metodo pratico `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-708">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="ec35e-709">Può essere usato per sostituire:</span><span class="sxs-lookup"><span data-stu-id="ec35e-709">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ec35e-710">`UseMvc` e `UseMvcWithDefaultRoute` aggiungono un'istanza di `RouterMiddleware` alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="ec35e-710">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="ec35e-711">MVC non interagisce direttamente con il middleware e usa il routing per gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="ec35e-711">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="ec35e-712">MVC è connesso alle route attraverso un'istanza di `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-712">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="ec35e-713">Il codice all'interno di `UseMvc` è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-713">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="ec35e-714">`UseMvc` non definisce direttamente le route, aggiunge un segnaposto alla raccolta di route per la route `attribute`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-714">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="ec35e-715">L'overload `UseMvc(Action<IRouteBuilder>)` consente di aggiungere le proprie route e supporta anche il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-715">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="ec35e-716">`UseMvc`e tutte le relative varianti aggiungono un segnaposto per l'attributo Route: il routing degli attributi è sempre disponibile indipendentemente dalla configurazione `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-716">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="ec35e-717">`UseMvcWithDefaultRoute` definisce una route predefinita e supporta il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-717">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="ec35e-718">La sezione [Routing con attributi](#attribute-routing-ref-label) include maggiori dettagli sul routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-718">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="ec35e-719">Routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-719">Conventional routing</span></span>

<span data-ttu-id="ec35e-720">La route predefinita (`default`):</span><span class="sxs-lookup"><span data-stu-id="ec35e-720">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="ec35e-721">Il codice precedente è un esempio di routing convenzionale.</span><span class="sxs-lookup"><span data-stu-id="ec35e-721">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="ec35e-722">Questo stile è denominato routing convenzionale perché stabilisce una *convenzione* per i percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="ec35e-722">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="ec35e-723">Il primo segmento di percorso viene mappato al nome del controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-723">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="ec35e-724">Il secondo esegue il mapping al nome dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-724">The second maps to the action name.</span></span>
* <span data-ttu-id="ec35e-725">Il terzo segmento viene usato per un oggetto facoltativo `id` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-725">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="ec35e-726">`id`esegue il mapping a un'entità del modello.</span><span class="sxs-lookup"><span data-stu-id="ec35e-726">`id` maps to a model entity.</span></span>

<span data-ttu-id="ec35e-727">Usando la route `default`, il percorso URL `/Products/List` esegue il mapping all'azione `ProductsController.List` e `/Blog/Article/17` lo esegue a `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-727">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="ec35e-728">Questo mapping si basa **solo** sui nomi dell'azione e del controller e non su spazi dei nomi, percorsi dei file di origine o parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-728">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="ec35e-729">L'uso del routing convenzionale con la route predefinita consente di compilare rapidamente l'applicazione senza dover creare un nuovo modello di URL per ogni azione che si definisce.</span><span class="sxs-lookup"><span data-stu-id="ec35e-729">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="ec35e-730">Per un'applicazione con azioni di stile CRUD, la coerenza degli URL tra i controller può semplificare il codice e rendere l'interfaccia utente più prevedibile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-730">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="ec35e-731">L'oggetto `id` è definito come facoltativo nel modello di route, ovvero le azioni possono essere eseguite senza l'ID specificato come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-731">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="ec35e-732">In genere, se si omette `id` dall'URL, il valore viene impostato su `0` dall'associazione del modello e di conseguenza non viene rilevata alcuna entità nel database corrispondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-732">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="ec35e-733">Il routing con attributi offre un controllo con granularità fine che consente di rendere l'ID obbligatorio per alcune azioni e non per altre.</span><span class="sxs-lookup"><span data-stu-id="ec35e-733">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="ec35e-734">Per convenzione la documentazione include i parametri facoltativi come `id` quando è probabile che appaiano nell'uso corretto.</span><span class="sxs-lookup"><span data-stu-id="ec35e-734">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="ec35e-735">Route multiple</span><span class="sxs-lookup"><span data-stu-id="ec35e-735">Multiple routes</span></span>

<span data-ttu-id="ec35e-736">È possibile aggiungere più route all'interno di `UseMvc` aggiungendo altre chiamate a `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-736">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="ec35e-737">Questa operazione consente di definire più convenzioni o di aggiungere route convenzionali dedicate a un'azione specifica, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ec35e-737">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ec35e-738">La route `blog` è una *route convenzionale dedicata*, vale a dire che usa il sistema di routing convenzionale, ma è dedicata a un'azione specifica.</span><span class="sxs-lookup"><span data-stu-id="ec35e-738">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="ec35e-739">Poiché `controller` e `action` non appaiono nel modello di route come parametri, possono avere solo i valori predefiniti e quindi questa route eseguirà sempre il mapping all'azione `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-739">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="ec35e-740">Le route sono ordinate nella raccolta di route e verranno elaborate nell'ordine in cui vengono aggiunte.</span><span class="sxs-lookup"><span data-stu-id="ec35e-740">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="ec35e-741">Quindi in questo esempio la route `blog` viene tentata prima della route `default`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-741">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-742">Le *Route convenzionali dedicate* usano spesso parametri di route **catch-all** come `{*article}` per acquisire la parte rimanente del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-742">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="ec35e-743">In questo modo una route può diventare troppo "greedy", ovvero corrispondere agli URL che dovrebbero corrispondere ad altre route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-743">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="ec35e-744">Posizionare le route "greedy" più avanti nella tabella delle route per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="ec35e-744">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="ec35e-745">Fallback</span><span class="sxs-lookup"><span data-stu-id="ec35e-745">Fallback</span></span>

<span data-ttu-id="ec35e-746">Come parte dell'elaborazione della richiesta, MVC verifica che i valori di route possano essere usati per trovare un controller e un'azione nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-746">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="ec35e-747">Se i valori di route non corrispondano a un'azione, la route non viene considerata una corrispondenza e viene tentata la route successiva.</span><span class="sxs-lookup"><span data-stu-id="ec35e-747">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="ec35e-748">Questa operazione si chiama *fallback* e viene eseguita allo scopo di semplificare i casi in cui le route convenzionali si sovrappongono.</span><span class="sxs-lookup"><span data-stu-id="ec35e-748">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="ec35e-749">Rimozione dell'ambiguità delle azioni</span><span class="sxs-lookup"><span data-stu-id="ec35e-749">Disambiguating actions</span></span>

<span data-ttu-id="ec35e-750">Quando due azioni corrispondono in base al routing, MVC deve rimuovere l'ambiguità per scegliere il candidato migliore, altrimenti genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-750">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="ec35e-751">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="ec35e-751">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="ec35e-752">Questo controller definisce due azioni corrispondenti al percorso URL `/Products/Edit/17` e indirizzano i dati `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-752">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="ec35e-753">Si tratta di un modello tipico per i controller MVC in cui `Edit(int)` visualizza un modulo per modificare un prodotto e `Edit(int, Product)` elabora il modulo inviato.</span><span class="sxs-lookup"><span data-stu-id="ec35e-753">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="ec35e-754">Per poter eseguire l'operazione, MVC deve scegliere `Edit(int, Product)` se la richiesta è un `POST` HTTP e `Edit(int)` quando il verbo HTTP è un altro elemento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-754">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="ec35e-755">`HttpPostAttribute` (`[HttpPost]`) è un'implementazione di `IActionConstraint` che consente di selezionare l'azione solo quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-755">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ec35e-756">La presenza di un oggetto `IActionConstraint` fa sì che `Edit(int, Product)` sia una corrispondenza migliore di `Edit(int)`, quindi `Edit(int, Product)` viene tentata per prima.</span><span class="sxs-lookup"><span data-stu-id="ec35e-756">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="ec35e-757">È necessario scrivere implementazioni personalizzate di `IActionConstraint` solo in scenari specializzati, ma è importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute`. Attributi simili vengono definiti per altri verbi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-757">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="ec35e-758">Nel routing convenzionale è normale che le azioni usino lo stesso nome di azione quando fanno parte di un flusso di lavoro `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-758">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="ec35e-759">La comodità offerta da questo modello sarà più evidente dopo aver esaminato la sezione relativa a [IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="ec35e-759">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="ec35e-760">Se vi sono più route corrispondenti e MVC non è in grado di trovare la route migliore, viene generata un'eccezione `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-760">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="ec35e-761">Nomi di route</span><span class="sxs-lookup"><span data-stu-id="ec35e-761">Route names</span></span>

<span data-ttu-id="ec35e-762">Le stringhe `"blog"` e `"default"` degli esempi seguenti sono nomi di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-762">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ec35e-763">I nomi di route consentono di assegnare a una route un nome logico, in modo che la route denominata possa essere usata per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-763">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="ec35e-764">Questo semplifica notevolmente la creazione degli URL quando l'ordinamento delle route può rendere complessa la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-764">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="ec35e-765">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-765">Route names must be unique application-wide.</span></span>

<span data-ttu-id="ec35e-766">I nomi di route non influiscono sulla corrispondenza degli URL o sulla gestione delle richieste, vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-766">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="ec35e-767">L'articolo sul [routing](xref:fundamentals/routing) contiene informazioni dettagliate sulla generazione di URL, inclusa la generazione negli helper specifici di MVC.</span><span class="sxs-lookup"><span data-stu-id="ec35e-767">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="ec35e-768">Routing con attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-768">Attribute routing</span></span>

<span data-ttu-id="ec35e-769">Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-769">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="ec35e-770">Nell'esempio seguente viene usato l'oggetto `app.UseMvc();` nel metodo `Configure` e non viene passata alcuna route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-770">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="ec35e-771">`HomeController` corrisponde a un set di URL simile a quello a cui corrisponderebbe la route predefinita `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-771">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="ec35e-772">L'azione `HomeController.Index()` verrà eseguita per tutti i percorsi URL, `/`, `/Home` o `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-772">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-773">In questo esempio viene evidenziata un'importante differenza a livello di programmazione tra il routing con attributi e il routing convenzionale.</span><span class="sxs-lookup"><span data-stu-id="ec35e-773">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="ec35e-774">Il routing con attributi richiede più input per specificare una route, la route convenzionale predefinita gestisce le route in modo più conciso.</span><span class="sxs-lookup"><span data-stu-id="ec35e-774">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="ec35e-775">Tuttavia, il routing con attributi consente, e richiede, un controllo preciso dei modelli route da applicare a ogni azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-775">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="ec35e-776">Con il routing con attributi i nomi del controller e delle azioni **non** hanno alcun ruolo nella selezione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-776">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="ec35e-777">In questo esempio viene verificata la corrispondenza degli stessi URL dell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-777">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="ec35e-778">I modelli di route precedenti non definiscono i parametri di route per `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-778">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="ec35e-779">In realtà, questi parametri di route non sono consentiti nelle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-779">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="ec35e-780">Poiché il modello di route è già associato a un'azione, non avrebbe senso analizzare il nome dell'azione dall'URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-780">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="ec35e-781">Routing con attributi Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="ec35e-781">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="ec35e-782">Il routing con attributi può usare anche gli attributi `Http[Verb]`, ad esempio `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-782">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="ec35e-783">Tutti questi attributi possono accettare un modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-783">All of these attributes can accept a route template.</span></span> <span data-ttu-id="ec35e-784">Questo esempio illustra due azioni che corrispondono allo stesso modello di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-784">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="ec35e-785">Per un percorso URL come `/products` l'azione `ProductsApi.ListProducts` verrà eseguita quando il verbo HTTP è `GET` e `ProductsApi.CreateProduct` verrà eseguita quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-785">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="ec35e-786">Nel routing con attributi l'URL viene prima confrontato con il set di modelli di route definiti dagli attributi della route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-786">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="ec35e-787">Quando un modello di route corrisponde, vengono applicati i vincoli `IActionConstraint` per determinare quali azioni possono essere eseguite.</span><span class="sxs-lookup"><span data-stu-id="ec35e-787">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="ec35e-788">Quando si compila un'API REST, è raro che si voglia usare `[Route(...)]` su un metodo di azione perché l'azione accetta tutti i metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-788">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="ec35e-789">Si consiglia di usare l'oggetto `Http*Verb*Attributes`, più specifico, per essere precisi circa gli elementi supportati dall'API.</span><span class="sxs-lookup"><span data-stu-id="ec35e-789">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="ec35e-790">I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.</span><span class="sxs-lookup"><span data-stu-id="ec35e-790">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="ec35e-791">Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-791">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="ec35e-792">In questo esempio `id` è richiesto come parte del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-792">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ec35e-793">L'azione `ProductsApi.GetProduct(int)` verrà eseguita per un percorso URL come `/products/3`, ma non per un percorso URL come `/products`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-793">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="ec35e-794">Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.</span><span class="sxs-lookup"><span data-stu-id="ec35e-794">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="ec35e-795">Nome di route</span><span class="sxs-lookup"><span data-stu-id="ec35e-795">Route Name</span></span>

<span data-ttu-id="ec35e-796">Nell’esempio di codice riportato di seguito viene definito un *nome di route* di `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-796">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ec35e-797">I nomi di route possono essere usati per generare un URL in base a un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="ec35e-797">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="ec35e-798">I nomi di route non influiscono sul comportamento del routing riguardo alla corrispondenza degli URL e vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-798">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="ec35e-799">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-799">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-800">Confrontare questa opzione con la *route predefinita* convenzionale, che definisce il parametro `id` come facoltativo (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="ec35e-800">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="ec35e-801">La possibilità di specificare in modo preciso le API presenta dei vantaggi, ad esempio consente di inviare `/products` e `/products/5` ad azioni  differenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-801">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="ec35e-802">Combinazione di route</span><span class="sxs-lookup"><span data-stu-id="ec35e-802">Combining routes</span></span>

<span data-ttu-id="ec35e-803">Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-803">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="ec35e-804">I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-804">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="ec35e-805">Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-805">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="ec35e-806">In questo esempio il percorso URL `/products` può corrispondere a `ProductsApi.ListProducts` e il percorso URL `/products/5` può corrispondere a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-806">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="ec35e-807">Entrambe le azioni corrispondono solo a HTTP `GET` perché sono contrassegnate con `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-807">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="ec35e-808">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-808">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="ec35e-809">Questo esempio illustra la corrispondenza di un set di percorsi URL simile alla *route predefinita*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-809">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="ec35e-810">Ordinamento delle route con attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-810">Ordering attribute routes</span></span>

<span data-ttu-id="ec35e-811">Diversamente dalle route convenzionali, che vengono eseguite in un ordine definito, il routing degli attributi compila un albero e trova la corrispondenza di tutte le route simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-811">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="ec35e-812">Si comporta come se le voci delle route seguissero un ordine ideale in cui le route più specifiche vengono probabilmente eseguite prima delle route più generali.</span><span class="sxs-lookup"><span data-stu-id="ec35e-812">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="ec35e-813">Ad esempio, una route come `blog/search/{topic}` è più specifica di una route come `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-813">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="ec35e-814">In termini logici la route `blog/search/{topic}` viene eseguita per prima, per impostazione predefinita, perché questo è l'ordine più plausibile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-814">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="ec35e-815">Usando il routing convenzionale, lo sviluppatore ordina le route in base alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="ec35e-815">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="ec35e-816">Nelle route con attributi si può configurare un ordine usando la proprietà `Order` di tutti gli attributi di route specificati dal framework.</span><span class="sxs-lookup"><span data-stu-id="ec35e-816">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="ec35e-817">Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-817">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="ec35e-818">L'ordine predefinito è `0`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-818">The default order is `0`.</span></span> <span data-ttu-id="ec35e-819">L'impostazione di una route usando `Order = -1` viene eseguita prima delle route per cui non è impostato un ordine.</span><span class="sxs-lookup"><span data-stu-id="ec35e-819">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="ec35e-820">L'impostazione di una route usando `Order = 1` viene eseguito dopo l'ordinamento predefinito delle route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-820">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="ec35e-821">Evitare la dipendenza da `Order`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-821">Avoid depending on `Order`.</span></span> <span data-ttu-id="ec35e-822">Se lo spazio URL richiede i valori in un ordine esplicito per indirizzare correttamente i dati, si può probabilmente creare confusione anche per i client.</span><span class="sxs-lookup"><span data-stu-id="ec35e-822">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="ec35e-823">In genere il routing con attributi seleziona la route corretta con la corrispondenza di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-823">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="ec35e-824">Se l'ordine predefinito usato per la generazione di URL non funziona, usare il nome della route come override è in genere più semplice che applicare la proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-824">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

Razor<span data-ttu-id="ec35e-825">Il routing di pagine e il routing del controller MVC condividono un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-825"> Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="ec35e-826">Le informazioni sull'ordine di route negli Razor argomenti pagine sono disponibili nelle [ Razor pagine Route e app Conventions: Order Route](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="ec35e-826">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="ec35e-827">Sostituzione dei token nei modelli di route ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="ec35e-827">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="ec35e-828">Per praticità, le route degli attributi supportano la *sostituzione dei token* mediante l'inclusione di un token tra parentesi quadre ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="ec35e-828">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="ec35e-829">I token `[action]`, `[area]` e `[controller]` vengono sostituiti con i valori di nome dell'azione, nome dell'area e nome del controller dall'azione in cui è definita la route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-829">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="ec35e-830">Nell'esempio seguente le azioni corrispondono ai percorsi URL come descritto nei commenti:</span><span class="sxs-lookup"><span data-stu-id="ec35e-830">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ec35e-831">La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-831">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="ec35e-832">L'esempio precedente si comporterà come il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-832">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="ec35e-833">Le route con attributi possono anche essere combinate con l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="ec35e-833">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="ec35e-834">Ciò risulta particolarmente efficace se combinato con la sostituzione dei token.</span><span class="sxs-lookup"><span data-stu-id="ec35e-834">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="ec35e-835">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-835">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="ec35e-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nome di route univoco per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-836">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="ec35e-837">Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).</span><span class="sxs-lookup"><span data-stu-id="ec35e-837">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="ec35e-838">Usare un trasformatore di parametri per personalizzare la sostituzione dei token</span><span class="sxs-lookup"><span data-stu-id="ec35e-838">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="ec35e-839">La sostituzione dei token può essere personalizzata usando un trasformatore di parametri.</span><span class="sxs-lookup"><span data-stu-id="ec35e-839">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="ec35e-840">Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri.</span><span class="sxs-lookup"><span data-stu-id="ec35e-840">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="ec35e-841">Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-841">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="ec35e-842">`RouteTokenTransformerConvention` è una convenzione del modello di applicazione che:</span><span class="sxs-lookup"><span data-stu-id="ec35e-842">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="ec35e-843">Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-843">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="ec35e-844">Personalizza i valori dei token delle route di attributi quando vengono sostituiti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-844">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="ec35e-845">La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-845">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="ec35e-846">Route multiple</span><span class="sxs-lookup"><span data-stu-id="ec35e-846">Multiple Routes</span></span>

<span data-ttu-id="ec35e-847">Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-847">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="ec35e-848">L'uso più comune è simulare il comportamento della *route convenzionale predefinita* come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-848">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="ec35e-849">Inserire più attributi di route nel controller significa che verranno tutti combinati con tutti gli attributi di route nei metodi dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-849">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="ec35e-850">Quando più attributi di route (che implementano `IActionConstraint`) sono inseriti in un'azione, ogni vincolo dell'azione viene combinato con il modello di route dall'attributo che lo definisce.</span><span class="sxs-lookup"><span data-stu-id="ec35e-850">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="ec35e-851">Benché l'uso di più route per le azioni possa sembrare efficace, è preferibile mantenere lo spazio URL dell'applicazione semplice e ben definito.</span><span class="sxs-lookup"><span data-stu-id="ec35e-851">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="ec35e-852">Usare più route per le azioni solo se necessario, ad esempio per supportare i client esistenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-852">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="ec35e-853">Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-853">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="ec35e-854">Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.</span><span class="sxs-lookup"><span data-stu-id="ec35e-854">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="ec35e-855">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-855">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="ec35e-856">Attributi di route personalizzati che usano `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="ec35e-856">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="ec35e-857">Tutti gli attributi di route disponibili nel framework (`[Route(...)]`, `[HttpGet(...)]` e così via) implementano l'interfaccia `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-857">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="ec35e-858">MVC cerca gli attributi per le classi del controller e i metodi delle azioni all'avvio dell'app e usa quelli che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-858">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="ec35e-859">È possibile implementare `IRouteTemplateProvider` per definire i propri attributi di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-859">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="ec35e-860">Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:</span><span class="sxs-lookup"><span data-stu-id="ec35e-860">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="ec35e-861">L'attributo dell'esempio precedente imposta automaticamente `Template` su `"api/[controller]"` quando si applica `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-861">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="ec35e-862">Uso del modello applicativo per personalizzare le route con attributi</span><span class="sxs-lookup"><span data-stu-id="ec35e-862">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="ec35e-863">Il *modello applicativo* è un modello a oggetti creato all'avvio con tutti i metadati usati da MVC per indirizzare ed eseguire le azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-863">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="ec35e-864">Il *modello applicativo* include tutti i dati raccolti da attributi di route (attraverso `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="ec35e-864">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="ec35e-865">È possibile scrivere *convenzioni* per modificare il modello applicativo in fase di avvio per personalizzare il comportamento del routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-865">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="ec35e-866">Questa sezione illustra un semplice esempio di personalizzazione del routing con il modello applicativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-866">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="ec35e-867">Routing misto: routing con attributi e routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="ec35e-867">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="ec35e-868">Le applicazioni MVC sono in grado di usare una combinazione di routing convenzionale e routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-868">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="ec35e-869">In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.</span><span class="sxs-lookup"><span data-stu-id="ec35e-869">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="ec35e-870">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-870">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="ec35e-871">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-871">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="ec35e-872">Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa.</span><span class="sxs-lookup"><span data-stu-id="ec35e-872">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="ec35e-873">**Qualsiasi** attributo di route del controller rende tutte le azioni presenti nel controller indirizzate con attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-873">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-874">Ciò che distingue i due tipi di sistema di routing è il processo applicato dopo che si è verificata la corrispondenza di un URL con un modello di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-874">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="ec35e-875">Nel routing convenzionale i valori di route della corrispondenza vengono usati per scegliere l'azione e il controller da una tabella di ricerca di tutte le azioni indirizzate in modo convenzionale.</span><span class="sxs-lookup"><span data-stu-id="ec35e-875">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="ec35e-876">Nel routing con attributi ogni modello è già associato a un'azione e non è necessaria alcuna ulteriore ricerca.</span><span class="sxs-lookup"><span data-stu-id="ec35e-876">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="ec35e-877">Segmenti complessi</span><span class="sxs-lookup"><span data-stu-id="ec35e-877">Complex segments</span></span>

<span data-ttu-id="ec35e-878">I segmenti complessi (ad esempio, `[Route("/dog{token}cat")]`), vengono elaborati individuando corrispondenze per i valori letterali da destra a sinistra in modalità non-greedy.</span><span class="sxs-lookup"><span data-stu-id="ec35e-878">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="ec35e-879">Vedere [il codice sorgente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) per una descrizione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-879">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="ec35e-880">Per altre informazioni, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="ec35e-880">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="ec35e-881">Generazione di URL</span><span class="sxs-lookup"><span data-stu-id="ec35e-881">URL Generation</span></span>

<span data-ttu-id="ec35e-882">Le applicazioni MVC sono in grado di usare le funzionalità di generazione di URL del routing per generare i collegamenti URL alle azioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-882">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="ec35e-883">La generazione di URL evita la codifica degli URL e consente di rendere il codice più affidabile e gestibile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-883">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="ec35e-884">Questa sezione illustra le funzionalità di generazione di URL disponibili in MVC e tratta solo le nozioni di base sul funzionamento della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-884">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="ec35e-885">Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-885">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="ec35e-886">L'interfaccia `IUrlHelper` è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-886">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="ec35e-887">È possibile trovare un'istanza di `IUrlHelper` disponibile usando la proprietà `Url` in controller, visualizzazioni e componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-887">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="ec35e-888">In questo esempio l'interfaccia `IUrlHelper` viene usata attraverso la proprietà `Controller.Url` per generare un URL a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-888">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="ec35e-889">Se l'applicazione usa la route convenzionale predefinita, il valore della variabile `url` sarà la stringa del percorso URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-889">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="ec35e-890">Il percorso URL viene creato dal routing combinando i valori di route della richiesta corrente (valori di ambiente) con i valori passati a `Url.Action` e sostituendo tali valori nel modello di route:</span><span class="sxs-lookup"><span data-stu-id="ec35e-890">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="ec35e-891">Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-891">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="ec35e-892">Per un parametro di route senza un valore si può usare un valore predefinito, se ne esiste uno, oppure ignorare il parametro se è facoltativo (come nel caso di `id` in questo esempio).</span><span class="sxs-lookup"><span data-stu-id="ec35e-892">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="ec35e-893">La generazione di URL avrà esito negativo se un parametro di route obbligatorio non ha un valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-893">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="ec35e-894">Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-894">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="ec35e-895">Nell'esempio di `Url.Action` precedente si presuppone che il routing sia convenzionale, ma la generazione di URL funziona in modo analogo con il routing con attributi, sebbene i concetti siano differenti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-895">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="ec35e-896">Con il routing convenzionale i valori di route vengono usati per espandere un modello e i valori di route per `controller` e `action` in genere appaiono in quel modello. Questo procedimento funziona perché gli URL corrispondenti in base al routing rispettano una *convenzione*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-896">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="ec35e-897">Nel routing con attributi i valori di route per `controller` e `action` non possono essere visualizzati nel modello ma vengono usati per individuare il modello da usare.</span><span class="sxs-lookup"><span data-stu-id="ec35e-897">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="ec35e-898">Questo esempio usa il routing con attributi:</span><span class="sxs-lookup"><span data-stu-id="ec35e-898">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="ec35e-899">MVC compila una tabella di ricerca di tutte le azioni indirizzate con attributi e stabilisce la corrispondenza dei valori `controller` e `action` per selezionare il modello di route da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-899">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="ec35e-900">Nell'esempio precedente viene generato `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-900">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="ec35e-901">Generazione di URL in base al nome dell'azione</span><span class="sxs-lookup"><span data-stu-id="ec35e-901">Generating URLs by action name</span></span>

<span data-ttu-id="ec35e-902">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="ec35e-902">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="ec35e-903">`Action`) e tutti gli overload correlati sono basati sull'idea che si voglia definire l'oggetto a cui ci si sta collegando specificando un nome di controller e un nome di azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-903">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-904">Quando si usa `Url.Action`, i valori di route correnti per `controller` e `action` vengono specificati automaticamente: i valori di `controller` e `action` fanno parte sia dei *valori di ambiente* \*\*sia dei \*\* *valori*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-904">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="ec35e-905">Il metodo `Url.Action`, usa sempre i valori correnti di `action` e `controller` e genererà un percorso URL che indirizza all'azione corrente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-905">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="ec35e-906">Il routing tenta di usare i valori nei valori di ambiente per ottenere le informazioni non specificate durante la generazione di un URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-906">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="ec35e-907">Usando una route come `{a}/{b}/{c}/{d}` e i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi, poiché tutti i parametri di route hanno un valore.</span><span class="sxs-lookup"><span data-stu-id="ec35e-907">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="ec35e-908">Se è stato aggiunto il valore `{ d = Donovan }`, il valore `{ d = David }` viene ignorato e il percorso URL generato è `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-908">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="ec35e-909">I percorsi URL sono gerarchici.</span><span class="sxs-lookup"><span data-stu-id="ec35e-909">URL paths are hierarchical.</span></span> <span data-ttu-id="ec35e-910">Nell'esempio precedente, se è stato aggiunto il valore `{ c = Cheryl }`, entrambi i valori `{ c = Carol, d = David }` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-910">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="ec35e-911">In questo caso non è più disponibile un valore per `d` e la generazione dell'URL avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-911">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="ec35e-912">Sarà necessario specificare un valore per `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-912">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="ec35e-913">In apparenza questo problema si può risolvere con la route predefinita (`{controller}/{action}/{id?}`), ma in realtà questo comportamento si verifica raramente perché `Url.Action` specifica sempre in modo esplicito un valore `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-913">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="ec35e-914">Anche gli overload di `Url.Action` di maggiore durata accettano anche un oggetto *valori di route* aggiuntivo per specificare i valori per i parametri di route diversi da `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-914">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="ec35e-915">In genere viene usato con `id` come `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-915">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="ec35e-916">Per convenzione l'oggetto *valori di route* è in genere un oggetto di tipo anonimo, ma può anche essere un `IDictionary<>` o un *normale oggetto .NET*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-916">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="ec35e-917">I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="ec35e-917">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="ec35e-918">Per creare un URL assoluto, usare un overload che accetta un `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="ec35e-918">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="ec35e-919">Generazione di URL in base alla route</span><span class="sxs-lookup"><span data-stu-id="ec35e-919">Generating URLs by route</span></span>

<span data-ttu-id="ec35e-920">Il codice precedente illustra come generare un URL passando il nome del controller e dell'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-920">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="ec35e-921">`IUrlHelper` specifica anche la famiglia di metodi `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-921">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="ec35e-922">Questi metodi sono simili a `Url.Action`, ma non copiano i valori correnti di `action` e `controller` nei valori di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-922">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="ec35e-923">L'uso più comune consiste nello specificare un nome di route per usare un percorso specifico per generare l'URL, in genere *senza* specificare un nome di controller o azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-923">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="ec35e-924">Generazione di URL in HTML</span><span class="sxs-lookup"><span data-stu-id="ec35e-924">Generating URLs in HTML</span></span>

<span data-ttu-id="ec35e-925">`IHtmlHelper` specifica i metodi `HtmlHelper``Html.BeginForm` e `Html.ActionLink` per generare rispettivamente gli elementi `<form>` e `<a>`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-925">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="ec35e-926">Questi metodi usano il metodo `Url.Action` per generare un URL e accettano argomenti simili.</span><span class="sxs-lookup"><span data-stu-id="ec35e-926">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="ec35e-927">Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="ec35e-927">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="ec35e-928">Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-928">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="ec35e-929">Entrambi usano `IUrlHelper` per la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-929">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="ec35e-930">Per altre informazioni, vedere [Uso dei moduli](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="ec35e-930">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="ec35e-931">All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="ec35e-931">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="ec35e-932">Generazione di URL nei risultati delle azioni</span><span class="sxs-lookup"><span data-stu-id="ec35e-932">Generating URLS in Action Results</span></span>

<span data-ttu-id="ec35e-933">Negli esempi precedenti è stato usato `IUrlHelper` in un controller, ma l'uso più comune in un controller è generare un URL come parte del risultato di un'azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-933">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="ec35e-934">Le classi di base `ControllerBase` e `Controller` offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-934">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="ec35e-935">Un uso tipico è eseguire il reindirizzamento dopo aver accettato l'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-935">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="ec35e-936">I metodi factory dei risultati delle azioni seguono un modello simile ai metodi per `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-936">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="ec35e-937">Caso speciale per le route convenzionali dedicate</span><span class="sxs-lookup"><span data-stu-id="ec35e-937">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="ec35e-938">Nel routing convenzionale è possibile usare un tipo speciale di definizione di route denominata *route convenzionale dedicata*.</span><span class="sxs-lookup"><span data-stu-id="ec35e-938">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="ec35e-939">Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata.</span><span class="sxs-lookup"><span data-stu-id="ec35e-939">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="ec35e-940">Usando queste definizioni di route, `Url.Action("Index", "Home")` genera il percorso URL `/` con la route `default` per un motivo ben preciso.</span><span class="sxs-lookup"><span data-stu-id="ec35e-940">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="ec35e-941">Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-941">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="ec35e-942">Le route convenzionali dedicate si basano su un comportamento speciale dei valori predefiniti per i quali non esiste un parametro di route corrispondente che impedisce alla route di essere troppo "greedy" con la generazione degli URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-942">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="ec35e-943">In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-943">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="ec35e-944">Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="ec35e-944">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="ec35e-945">La generazione di URL che usa `blog` avrà esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-945">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="ec35e-946">Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-946">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="ec35e-947">Aree</span><span class="sxs-lookup"><span data-stu-id="ec35e-947">Areas</span></span>

<span data-ttu-id="ec35e-948">Le [aree](areas.md) sono una funzionalità di MVC che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi di routing separato (per le azioni del controller) e struttura di cartelle (per le visualizzazioni).</span><span class="sxs-lookup"><span data-stu-id="ec35e-948">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="ec35e-949">L'uso delle aree consente a un'applicazione di usare più controller con lo stesso nome, purché abbiano *aree* diverse.</span><span class="sxs-lookup"><span data-stu-id="ec35e-949">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="ec35e-950">Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-950">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="ec35e-951">Questa sezione illustra il modo in cui il routing interagisce con le aree. Vedere [Aree](areas.md) per informazioni dettagliate sull'uso delle aree con le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="ec35e-951">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="ec35e-952">Nell'esempio seguente MVC viene configurato in modo da usare la route convenzionale predefinita e una *route di area* per un'area denominata `Blog`:</span><span class="sxs-lookup"><span data-stu-id="ec35e-952">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="ec35e-953">Nella corrispondenza di un percorso URL come `/Manage/Users/AddUser` la prima route produrrà i valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-953">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="ec35e-954">Il valore di route `area` è generato da un valore predefinito per `area`, infatti la route creata da `MapAreaRoute` equivale alla seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-954">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="ec35e-955">`MapAreaRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-955">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="ec35e-956">Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-956">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="ec35e-957">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="ec35e-957">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ec35e-958">In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="ec35e-958">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ec35e-959">Se si usa l'esempio precedente, i valori di route corrispondono all'azione seguente:</span><span class="sxs-lookup"><span data-stu-id="ec35e-959">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="ec35e-960">`AreaAttribute` è ciò che denota un controller come parte di un'area, questo controller in particolare si trova nell'area `Blog`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-960">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="ec35e-961">I controller senza un attributo `[Area]` non sono membri di alcuna area e **non** corrispondono quando il valore di route `area` viene specificato dal routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-961">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="ec35e-962">Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-962">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="ec35e-963">Lo spazio dei nomi di ogni controller viene indicato qui per motivi di completezza, per evitare conflitti di denominazione nei controller e la generazione di errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="ec35e-963">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="ec35e-964">Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.</span><span class="sxs-lookup"><span data-stu-id="ec35e-964">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="ec35e-965">I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-965">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="ec35e-966">Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.</span><span class="sxs-lookup"><span data-stu-id="ec35e-966">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-967">In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="ec35e-967">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="ec35e-968">Quando si esegue un'azione all'interno di un'area, il valore di route per `area` sarà disponibile come *valore di ambiente* per il routing da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-968">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="ec35e-969">Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="ec35e-969">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="ec35e-970">Informazioni su IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="ec35e-970">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="ec35e-971">Questa sezione offre un'analisi approfondita degli elementi interni del framework e del modo in cui MVC sceglie un'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="ec35e-971">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="ec35e-972">Un'applicazione tipica non richiede un oggetto `IActionConstraint` personalizzato</span><span class="sxs-lookup"><span data-stu-id="ec35e-972">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="ec35e-973">Probabilmente l'utente ha già usato `IActionConstraint` anche se non ha familiarità con l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="ec35e-973">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="ec35e-974">L'attributo `[HttpGet]` e gli attributi `[Http-VERB]` dello stesso tipo implementano `IActionConstraint` per limitare l'esecuzione di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-974">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="ec35e-975">Se si usa la route convenzionale predefinita, il percorso URL `/Products/Edit` produce i valori `{ controller = Products, action = Edit }`, che corrispondono a **entrambe** le azioni illustrate qui.</span><span class="sxs-lookup"><span data-stu-id="ec35e-975">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="ec35e-976">Nella terminologia di `IActionConstraint` si dice che entrambe le azioni sono considerate candidati poiché corrispondono entrambe ai dati della route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-976">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="ec35e-977">Quando viene eseguito `HttpGetAttribute` indica che *Edit()* è una corrispondenza per *GET* e non è una corrispondenza per qualsiasi altro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-977">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="ec35e-978">L'azione `Edit(...)` non ha tutti i vincoli definiti e quindi corrisponde a qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-978">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="ec35e-979">Presupponendo l'uso di un oggetto `POST`, corrisponde solo `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-979">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="ec35e-980">Per un oggetto `GET` possono invece corrispondere entrambe le azioni, tuttavia un'azione con `IActionConstraint` viene sempre considerata *migliore* rispetto a un'azione senza tale oggetto.</span><span class="sxs-lookup"><span data-stu-id="ec35e-980">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="ec35e-981">Quindi, dal momento che `Edit()` ha `[HttpGet]` è considerata più specifica e verrà selezionata se entrambe le azioni possono corrispondere.</span><span class="sxs-lookup"><span data-stu-id="ec35e-981">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="ec35e-982">Concettualmente, `IActionConstraint` è una forma di *overload*, ma anziché sostituire i metodi con lo stesso nome, supporta l'overload tra le azioni che corrispondono allo stesso URL.</span><span class="sxs-lookup"><span data-stu-id="ec35e-982">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="ec35e-983">Anche il routing con attributi usa `IActionConstraint` e può accadere che azioni di controller diversi vengano entrambe considerate candidati.</span><span class="sxs-lookup"><span data-stu-id="ec35e-983">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="ec35e-984">Implementazione di IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="ec35e-984">Implementing IActionConstraint</span></span>

<span data-ttu-id="ec35e-985">Il modo più semplice di implementare un oggetto `IActionConstraint` è creare una classe derivata da `System.Attribute` e inserirla nelle azioni e nel controller.</span><span class="sxs-lookup"><span data-stu-id="ec35e-985">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="ec35e-986">MVC rileverà automaticamente tutti gli oggetti `IActionConstraint` applicati come attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-986">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="ec35e-987">È possibile usare il modello di applicazione per applicare i vincoli e questo è probabilmente l'approccio più flessibile, poiché consente di metaprogrammare le modalità di applicazione.</span><span class="sxs-lookup"><span data-stu-id="ec35e-987">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="ec35e-988">Nell'esempio seguente un vincolo sceglie un'azione in base a un *codice paese* dei dati della route.</span><span class="sxs-lookup"><span data-stu-id="ec35e-988">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="ec35e-989">Vedere l'[esempio completo su GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="ec35e-989">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="ec35e-990">L'utente è responsabile dell'implementazione del metodo `Accept` e della scelta di un "ordine" per il vincolo da eseguire.</span><span class="sxs-lookup"><span data-stu-id="ec35e-990">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="ec35e-991">In questo caso il metodo `Accept` restituisce `true` per indicare che l'azione è una corrispondenza quando il valore di route `country` corrisponde.</span><span class="sxs-lookup"><span data-stu-id="ec35e-991">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="ec35e-992">Questo comportamento è diverso da quello di `RouteValueAttribute` poiché viene consentito il fallback a un'azione senza attributi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-992">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="ec35e-993">L'esempio indica che se si definisce un'azione `en-US`, per il codice paese, ad esempio `fr-FR`, viene eseguito il fallback a un controller più generico a cui non è applicato `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-993">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="ec35e-994">La proprietà `Order` decide a quale *fase* appartiene il vincolo.</span><span class="sxs-lookup"><span data-stu-id="ec35e-994">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="ec35e-995">I vincoli relativi alle azioni vengono eseguiti in gruppi basati su `Order`.</span><span class="sxs-lookup"><span data-stu-id="ec35e-995">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="ec35e-996">Ad esempio, tutti gli attributi del metodo HTTP specificati dal framework usano lo stesso valore `Order` in modo da essere eseguiti nella stessa fase.</span><span class="sxs-lookup"><span data-stu-id="ec35e-996">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="ec35e-997">È possibile usare un numero illimitato di fasi per implementare i criteri necessari.</span><span class="sxs-lookup"><span data-stu-id="ec35e-997">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="ec35e-998">Quando si sceglie un valore per `Order` considerare se il vincolo dovrà essere applicato o meno prima dei metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="ec35e-998">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="ec35e-999">I numeri più bassi vengono eseguiti per primi.</span><span class="sxs-lookup"><span data-stu-id="ec35e-999">Lower numbers run first.</span></span>

::: moniker-end
