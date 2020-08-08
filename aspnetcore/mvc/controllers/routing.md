---
title: Routing ad azioni del controller in ASP.NET Core
author: rick-anderson
description: Informazioni su come ASP.NET Core MVC usa middleware di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 4d367a6b15fdcf9ef6be1bac749368fd48fa259e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020366"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="26008-103">Routing ad azioni del controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26008-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="26008-104">Di [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5)e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="26008-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26008-105">I controller di ASP.NET Core usano il [middleware](xref:fundamentals/middleware/index) di routing per trovare le corrispondenze con gli URL delle richieste in ingresso ed eseguirne il mapping alle [azioni](#action).</span><span class="sxs-lookup"><span data-stu-id="26008-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="26008-106">Modelli di route:</span><span class="sxs-lookup"><span data-stu-id="26008-106">Routes templates:</span></span>

* <span data-ttu-id="26008-107">Sono definiti nel codice di avvio o negli attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="26008-108">Descrizione della corrispondenza dei percorsi URL con le [azioni](#action).</span><span class="sxs-lookup"><span data-stu-id="26008-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="26008-109">Vengono usati per generare URL per i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="26008-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="26008-110">I collegamenti generati vengono in genere restituiti nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="26008-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="26008-111">Le azioni sono indirizzate [convenzionalmente](#cr) o [indirizzate agli attributi](#ar).</span><span class="sxs-lookup"><span data-stu-id="26008-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="26008-112">L'inserimento di una route sul controller o sull' [azione](#action) lo rende instradato con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="26008-113">Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="26008-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="26008-114">Questo documento:</span><span class="sxs-lookup"><span data-stu-id="26008-114">This document:</span></span>

* <span data-ttu-id="26008-115">Illustra le interazioni tra MVC e il routing:</span><span class="sxs-lookup"><span data-stu-id="26008-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="26008-116">Il modo in cui le app MVC tipiche usano le funzionalità di routing.</span><span class="sxs-lookup"><span data-stu-id="26008-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="26008-117">Vengono illustrati entrambi:</span><span class="sxs-lookup"><span data-stu-id="26008-117">Covers both:</span></span>
    * <span data-ttu-id="26008-118">[Routing convenzionale](#cr) usato in genere con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="26008-118">[Conventionally routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="26008-119">*Routing degli attributi* usato con le API REST.</span><span class="sxs-lookup"><span data-stu-id="26008-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="26008-120">Se si è interessati principalmente al routing per le API REST, passare alla sezione [routing degli attributi per le API REST](#ar) .</span><span class="sxs-lookup"><span data-stu-id="26008-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="26008-121">Vedere [routing](xref:fundamentals/routing) per i dettagli del routing avanzato.</span><span class="sxs-lookup"><span data-stu-id="26008-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="26008-122">Si riferisce al sistema di routing predefinito aggiunto in ASP.NET Core 3,0, denominato endpoint routing.</span><span class="sxs-lookup"><span data-stu-id="26008-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="26008-123">È possibile usare i controller con la versione precedente di routing per motivi di compatibilità.</span><span class="sxs-lookup"><span data-stu-id="26008-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="26008-124">Per istruzioni, vedere la [Guida alla migrazione 2.2-3.0](xref:migration/22-to-30) .</span><span class="sxs-lookup"><span data-stu-id="26008-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="26008-125">Per materiale di riferimento sul sistema di routing legacy, vedere la [versione 2,2 di questo documento](xref:mvc/controllers/routing?view=aspnetcore-2.2) .</span><span class="sxs-lookup"><span data-stu-id="26008-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="26008-126">Configurare la route convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-126">Set up conventional route</span></span>

<span data-ttu-id="26008-127">`Startup.Configure`Quando si usa il [routing convenzionale](#crd), in genere il codice è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="26008-128">All'interno della chiamata a <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> viene usato per creare una singola route.</span><span class="sxs-lookup"><span data-stu-id="26008-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="26008-129">La route singola è denominata `default` Route.</span><span class="sxs-lookup"><span data-stu-id="26008-129">The single route is named `default` route.</span></span> <span data-ttu-id="26008-130">La maggior parte delle app con controller e visualizzazioni usa un modello di route simile a quello della `default` Route.</span><span class="sxs-lookup"><span data-stu-id="26008-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="26008-131">Le API REST devono usare il [routing degli attributi](#ar).</span><span class="sxs-lookup"><span data-stu-id="26008-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="26008-132">Il modello di route `"{controller=Home}/{action=Index}/{id?}"` :</span><span class="sxs-lookup"><span data-stu-id="26008-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="26008-133">Corrisponde a un percorso URL come`/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="26008-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="26008-134">Estrae i valori della route `{ controller = Products, action = Details, id = 5 }` suddivisione in token il percorso.</span><span class="sxs-lookup"><span data-stu-id="26008-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="26008-135">L'estrazione dei valori di route comporta una corrispondenza se l'app dispone di un controller denominato `ProductsController` e di un' `Details` azione:</span><span class="sxs-lookup"><span data-stu-id="26008-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="26008-136">`/Products/Details/5`il modello associa il valore di `id = 5` per impostare il `id` parametro su `5` .</span><span class="sxs-lookup"><span data-stu-id="26008-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="26008-137">Per ulteriori informazioni, vedere [associazione di modelli](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="26008-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="26008-138">`{controller=Home}`definisce `Home` come valore predefinito `controller` .</span><span class="sxs-lookup"><span data-stu-id="26008-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="26008-139">`{action=Index}`definisce `Index` come valore predefinito `action` .</span><span class="sxs-lookup"><span data-stu-id="26008-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="26008-140">Il `?` carattere in `{id?}` definisce `id` come facoltativo.</span><span class="sxs-lookup"><span data-stu-id="26008-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="26008-141">I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="26008-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="26008-142">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="26008-143">Corrisponde al percorso dell'URL `/` .</span><span class="sxs-lookup"><span data-stu-id="26008-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="26008-144">Produce i valori della route `{ controller = Home, action = Index }` .</span><span class="sxs-lookup"><span data-stu-id="26008-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="26008-145">I valori per `controller` e `action` usano i valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="26008-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="26008-146">`id`non produce un valore poiché non esiste un segmento corrispondente nel percorso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="26008-147">`/`corrisponde solo se esiste un' `HomeController` azione e `Index` :</span><span class="sxs-lookup"><span data-stu-id="26008-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="26008-148">Utilizzando la definizione del controller e il modello di route precedenti, l' `HomeController.Index` azione viene eseguita per i seguenti percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="26008-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="26008-149">Il percorso URL `/` Usa i controller e l'azione predefiniti del modello `Home` di route `Index` .</span><span class="sxs-lookup"><span data-stu-id="26008-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="26008-150">Il percorso URL `/Home` Usa l'azione predefinita del modello di route `Index` .</span><span class="sxs-lookup"><span data-stu-id="26008-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="26008-151">Il metodo pratico <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span><span class="sxs-lookup"><span data-stu-id="26008-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="26008-152">Sostituisce</span><span class="sxs-lookup"><span data-stu-id="26008-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="26008-153">Il routing viene configurato usando <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> il <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware e.</span><span class="sxs-lookup"><span data-stu-id="26008-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="26008-154">Per usare i controller:</span><span class="sxs-lookup"><span data-stu-id="26008-154">To use controllers:</span></span>
>
> * <span data-ttu-id="26008-155">Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> all'interno per eseguire il mapping tra i `UseEndpoints` controller [indirizzati degli attributi](#ar) .</span><span class="sxs-lookup"><span data-stu-id="26008-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="26008-156">Chiamare <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> o <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> per eseguire il mapping tra controller [indirizzati convenzionalmente](#cr) e controller [indirizzati degli attributi](#ar) .</span><span class="sxs-lookup"><span data-stu-id="26008-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="26008-157">Routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-157">Conventional routing</span></span>

<span data-ttu-id="26008-158">Il routing convenzionale viene usato con i controller e le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="26008-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="26008-159">La route predefinita (`default`):</span><span class="sxs-lookup"><span data-stu-id="26008-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="26008-160">è un esempio di *routing convenzionale*.</span><span class="sxs-lookup"><span data-stu-id="26008-160">is an example of a *conventional routing*.</span></span> <span data-ttu-id="26008-161">Viene chiamato *routing convenzionale* perché stabilisce una *convenzione* per i percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="26008-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="26008-162">Il primo segmento di percorso, `{controller=Home}` , esegue il mapping al nome del controller.</span><span class="sxs-lookup"><span data-stu-id="26008-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="26008-163">Il secondo segmento, `{action=Index}` , esegue il mapping al nome dell' [azione](#action) .</span><span class="sxs-lookup"><span data-stu-id="26008-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="26008-164">Il terzo segmento `{id?}` viene usato per un oggetto facoltativo `id` .</span><span class="sxs-lookup"><span data-stu-id="26008-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="26008-165">`?`In lo `{id?}` rende facoltativo.</span><span class="sxs-lookup"><span data-stu-id="26008-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="26008-166">`id`viene utilizzato per eseguire il mapping a un'entità del modello.</span><span class="sxs-lookup"><span data-stu-id="26008-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="26008-167">Utilizzando questa `default` Route, il percorso dell'URL:</span><span class="sxs-lookup"><span data-stu-id="26008-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="26008-168">`/Products/List`esegue il mapping all' `ProductsController.List` azione.</span><span class="sxs-lookup"><span data-stu-id="26008-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="26008-169">`/Blog/Article/17`esegue il mapping a `BlogController.Article` e in genere il modello associa il `id` parametro a 17.</span><span class="sxs-lookup"><span data-stu-id="26008-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="26008-170">Questo mapping:</span><span class="sxs-lookup"><span data-stu-id="26008-170">This mapping:</span></span>

* <span data-ttu-id="26008-171">Si basa **solo**sui nomi del controller e dell' [azione](#action) .</span><span class="sxs-lookup"><span data-stu-id="26008-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="26008-172">Non è basato su spazi dei nomi, percorsi di file di origine o parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="26008-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="26008-173">L'uso del routing convenzionale con la route predefinita consente di creare l'app senza dover ottenere un nuovo modello di URL per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="26008-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="26008-174">Per un'app con azioni di stile [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) , con coerenza per gli URL tra i controller:</span><span class="sxs-lookup"><span data-stu-id="26008-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="26008-175">Consente di semplificare il codice.</span><span class="sxs-lookup"><span data-stu-id="26008-175">Helps simplify the code.</span></span>
* <span data-ttu-id="26008-176">Rende l'interfaccia utente più prevedibile.</span><span class="sxs-lookup"><span data-stu-id="26008-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="26008-177">`id`Nel codice precedente viene definito come facoltativo dal modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="26008-178">Le azioni possono essere eseguite senza l'ID facoltativo fornito come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="26008-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="26008-179">In genere, quando `id` viene omesso dall'URL:</span><span class="sxs-lookup"><span data-stu-id="26008-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="26008-180">`id`viene impostato su `0` dall'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="26008-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="26008-181">Non è stata trovata alcuna entità nel database corrispondente `id == 0` .</span><span class="sxs-lookup"><span data-stu-id="26008-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="26008-182">Il [routing degli attributi](#ar) offre un controllo con granularità fine per rendere l'ID necessario per alcune azioni e non per altri.</span><span class="sxs-lookup"><span data-stu-id="26008-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="26008-183">Per convenzione, la documentazione include parametri facoltativi come `id` quando è probabile che vengano visualizzati nell'utilizzo corretto.</span><span class="sxs-lookup"><span data-stu-id="26008-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="26008-184">La maggior parte delle app dovrebbe scegliere uno schema di routing semplice e descrittivo in modo che gli URL siano leggibili e significativi.</span><span class="sxs-lookup"><span data-stu-id="26008-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="26008-185">La route convenzionale predefinita `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="26008-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="26008-186">Supporta uno schema di routing semplice e descrittivo.</span><span class="sxs-lookup"><span data-stu-id="26008-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="26008-187">È un punto iniziale utile per le app basate su interfaccia utente.</span><span class="sxs-lookup"><span data-stu-id="26008-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="26008-188">È l'unico modello di route necessario per molte app dell'interfaccia utente Web.</span><span class="sxs-lookup"><span data-stu-id="26008-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="26008-189">Per le app di interfaccia utente Web di dimensioni maggiori, un'altra route che usa le [aree](#areas) se di frequente è sufficiente.</span><span class="sxs-lookup"><span data-stu-id="26008-189">For larger web UI apps, another route using [Areas](#areas) if frequently all that's needed.</span></span>

<span data-ttu-id="26008-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>e <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span><span class="sxs-lookup"><span data-stu-id="26008-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="26008-191">Assegnare automaticamente un valore di **ordine** ai rispettivi endpoint in base all'ordine in cui vengono richiamati.</span><span class="sxs-lookup"><span data-stu-id="26008-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="26008-192">Routing degli endpoint in ASP.NET Core 3,0 e versioni successive:</span><span class="sxs-lookup"><span data-stu-id="26008-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="26008-193">Non ha un concetto di route.</span><span class="sxs-lookup"><span data-stu-id="26008-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="26008-194">Non fornisce garanzie di ordinamento per l'esecuzione dell'estensibilità, tutti gli endpoint vengono elaborati contemporaneamente.</span><span class="sxs-lookup"><span data-stu-id="26008-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="26008-195">Abilitare la [registrazione](xref:fundamentals/logging/index) per verificare in che modo le implementazioni del routing predefinite, ad esempio <xref:Microsoft.AspNetCore.Routing.Route>, corrispondono alle richieste.</span><span class="sxs-lookup"><span data-stu-id="26008-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="26008-196">Il [routing degli attributi](#ar) è illustrato più avanti in questo documento.</span><span class="sxs-lookup"><span data-stu-id="26008-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="26008-197">Più route convenzionali</span><span class="sxs-lookup"><span data-stu-id="26008-197">Multiple conventional routes</span></span>

<span data-ttu-id="26008-198">È possibile aggiungere più [Route convenzionali](#cr) all'interno `UseEndpoints` di aggiungendo altre chiamate a <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> e <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> .</span><span class="sxs-lookup"><span data-stu-id="26008-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="26008-199">In questo modo è possibile definire più convenzioni o aggiungere route convenzionali dedicate a un' [azione](#action)specifica, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="26008-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="26008-200">La `blog` Route nel codice precedente è una **Route convenzionale dedicata**.</span><span class="sxs-lookup"><span data-stu-id="26008-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="26008-201">Viene chiamato Route convenzionale dedicata perché:</span><span class="sxs-lookup"><span data-stu-id="26008-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="26008-202">Usa il [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="26008-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="26008-203">È dedicata a un' [azione](#action)specifica.</span><span class="sxs-lookup"><span data-stu-id="26008-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="26008-204">Poiché `controller` e `action` non vengono visualizzati nel modello di route `"blog/{*article}"` come parametri:</span><span class="sxs-lookup"><span data-stu-id="26008-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="26008-205">Possono avere solo i valori predefiniti `{ controller = "Blog", action = "Article" }` .</span><span class="sxs-lookup"><span data-stu-id="26008-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="26008-206">Questa route è sempre associata all'azione `BlogController.Article` .</span><span class="sxs-lookup"><span data-stu-id="26008-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="26008-207">`/Blog`, `/Blog/Article` e `/Blog/{any-string}` sono gli unici percorsi URL che corrispondono alla route del Blog.</span><span class="sxs-lookup"><span data-stu-id="26008-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="26008-208">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-208">The preceding example:</span></span>

* <span data-ttu-id="26008-209">`blog`la route ha una priorità più elevata per le corrispondenze rispetto alla `default` Route perché viene aggiunta per prima.</span><span class="sxs-lookup"><span data-stu-id="26008-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="26008-210">È un esempio di routing di tipo [Slug](https://developer.mozilla.org/docs/Glossary/Slug) in cui è tipico avere un nome di articolo come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="26008-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="26008-211">In ASP.NET Core 3,0 e versioni successive, il routing non:</span><span class="sxs-lookup"><span data-stu-id="26008-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="26008-212">Definire un concetto denominato *Route*.</span><span class="sxs-lookup"><span data-stu-id="26008-212">Define a concept called a *route*.</span></span> <span data-ttu-id="26008-213">`UseRouting`aggiunge la corrispondenza della route alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="26008-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="26008-214">Il `UseRouting` middleware esamina il set di endpoint definiti nell'app e seleziona la corrispondenza migliore per l'endpoint in base alla richiesta.</span><span class="sxs-lookup"><span data-stu-id="26008-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="26008-215">Fornire garanzie sull'ordine di esecuzione dell'estensibilità <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> , ad esempio o <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .</span><span class="sxs-lookup"><span data-stu-id="26008-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="26008-216">Vedere [routing](xref:fundamentals/routing) per il materiale di riferimento sul routing.</span><span class="sxs-lookup"><span data-stu-id="26008-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="26008-217">Ordine di routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-217">Conventional routing order</span></span>

<span data-ttu-id="26008-218">Il routing convenzionale corrisponde solo a una combinazione di azione e controller definiti dall'app.</span><span class="sxs-lookup"><span data-stu-id="26008-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="26008-219">Questa operazione è progettata per semplificare i casi in cui le route convenzionali si sovrappongono.</span><span class="sxs-lookup"><span data-stu-id="26008-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="26008-220">Se si aggiungono route usando <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> e si <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> assegna automaticamente un valore di ordine ai rispettivi endpoint in base all'ordine in cui vengono richiamati.</span><span class="sxs-lookup"><span data-stu-id="26008-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="26008-221">Corrisponde a una route visualizzata in precedenza con una priorità più alta.</span><span class="sxs-lookup"><span data-stu-id="26008-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="26008-222">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="26008-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="26008-223">In generale, le route con aree devono essere posizionate in precedenza perché sono più specifiche delle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="26008-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="26008-224">Le route [convenzionali dedicate](#dcr) con parametri di route catch-all come `{*article}` possono rendere troppo [greedy](xref:fundamentals/routing#greedy)una route, vale a dire che corrisponde agli URL per i quali si intende trovare una corrispondenza con altre route.</span><span class="sxs-lookup"><span data-stu-id="26008-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="26008-225">Inserire le route greedy in un secondo momento nella tabella di route per evitare corrispondenze greedy.</span><span class="sxs-lookup"><span data-stu-id="26008-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="26008-226">Risoluzione di azioni ambigue</span><span class="sxs-lookup"><span data-stu-id="26008-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="26008-227">Quando due endpoint corrispondono tramite il routing, il routing deve eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="26008-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="26008-228">Scegliere il candidato migliore.</span><span class="sxs-lookup"><span data-stu-id="26008-228">Choose the best candidate.</span></span>
* <span data-ttu-id="26008-229">Generazione di un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="26008-229">Throw an exception.</span></span>

<span data-ttu-id="26008-230">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="26008-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="26008-231">Il controller precedente definisce due azioni che corrispondono a:</span><span class="sxs-lookup"><span data-stu-id="26008-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="26008-232">Percorso URL`/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="26008-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="26008-233">Dati di route `{ controller = Products33, action = Edit, id = 17 }` .</span><span class="sxs-lookup"><span data-stu-id="26008-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="26008-234">Si tratta di un modello tipico per i controller MVC:</span><span class="sxs-lookup"><span data-stu-id="26008-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="26008-235">`Edit(int)`Visualizza un modulo per la modifica di un prodotto.</span><span class="sxs-lookup"><span data-stu-id="26008-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="26008-236">`Edit(int, Product)`elabora il form inviato.</span><span class="sxs-lookup"><span data-stu-id="26008-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="26008-237">Per risolvere la route corretta:</span><span class="sxs-lookup"><span data-stu-id="26008-237">To resolve the correct route:</span></span>

* <span data-ttu-id="26008-238">`Edit(int, Product)`viene selezionato quando la richiesta è un HTTP `POST` .</span><span class="sxs-lookup"><span data-stu-id="26008-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="26008-239">`Edit(int)`viene selezionato quando il [verbo http](#verb) è qualsiasi altro elemento.</span><span class="sxs-lookup"><span data-stu-id="26008-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="26008-240">`Edit(int)`viene in genere chiamato tramite `GET` .</span><span class="sxs-lookup"><span data-stu-id="26008-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="26008-241">L'oggetto <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , viene fornito al routing in modo che sia possibile scegliere in base al metodo HTTP della richiesta.</span><span class="sxs-lookup"><span data-stu-id="26008-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="26008-242">`HttpPostAttribute`Consente `Edit(int, Product)` di ottenere una corrispondenza migliore rispetto a `Edit(int)` .</span><span class="sxs-lookup"><span data-stu-id="26008-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="26008-243">È importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute` .</span><span class="sxs-lookup"><span data-stu-id="26008-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="26008-244">Attributi simili vengono definiti per altri [verbi HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="26008-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="26008-245">Nel [routing convenzionale](#cr), è comune per le azioni usare lo stesso nome di azione quando fanno parte di un flusso di lavoro di invio del modulo di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="26008-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="26008-246">Vedere ad esempio [esaminare i due metodi di azione di modifica](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span><span class="sxs-lookup"><span data-stu-id="26008-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="26008-247">Se il routing non è in grado di scegliere un candidato migliore, <xref:System.Reflection.AmbiguousMatchException> viene generata un'eccezione, che elenca più endpoint corrispondenti.</span><span class="sxs-lookup"><span data-stu-id="26008-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="26008-248">Nomi di route convenzionali</span><span class="sxs-lookup"><span data-stu-id="26008-248">Conventional route names</span></span>

<span data-ttu-id="26008-249">Le stringhe `"blog"` e `"default"` negli esempi seguenti sono nomi di route convenzionali:</span><span class="sxs-lookup"><span data-stu-id="26008-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="26008-250">I nomi di route assegnano alla route un nome logico.</span><span class="sxs-lookup"><span data-stu-id="26008-250">The route names give the route a logical name.</span></span> <span data-ttu-id="26008-251">La route denominata può essere usata per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="26008-252">L'uso di una route denominata semplifica la creazione di URL quando l'ordinamento delle route può rendere complessa la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="26008-253">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="26008-254">Nomi di route:</span><span class="sxs-lookup"><span data-stu-id="26008-254">Route names:</span></span>

* <span data-ttu-id="26008-255">Non hanno alcun effetto sulla corrispondenza degli URL o sulla gestione delle richieste.</span><span class="sxs-lookup"><span data-stu-id="26008-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="26008-256">Vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-256">Are used only for URL generation.</span></span>

<span data-ttu-id="26008-257">Il concetto di nome della route è rappresentato nel routing come [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span><span class="sxs-lookup"><span data-stu-id="26008-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="26008-258">Il **nome della route** e il nome dell' **endpoint**:</span><span class="sxs-lookup"><span data-stu-id="26008-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="26008-259">Sono intercambiabili.</span><span class="sxs-lookup"><span data-stu-id="26008-259">Are interchangeable.</span></span>
* <span data-ttu-id="26008-260">Quello usato nella documentazione e nel codice dipende dall'API descritta.</span><span class="sxs-lookup"><span data-stu-id="26008-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="26008-261">Routing degli attributi per le API REST</span><span class="sxs-lookup"><span data-stu-id="26008-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="26008-262">Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da [verbi HTTP](#verb).</span><span class="sxs-lookup"><span data-stu-id="26008-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="26008-263">Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="26008-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="26008-264">Il `StartUp.Configure` codice seguente è tipico per un'API REST e viene usato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="26008-265">Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> viene chiamato all'interno `UseEndpoints` di per eseguire il mapping dei controller indirizzati degli attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="26008-266">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-266">In the following example:</span></span>

* <span data-ttu-id="26008-267">`Configure`Viene utilizzato il metodo precedente.</span><span class="sxs-lookup"><span data-stu-id="26008-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="26008-268">`HomeController`corrisponde a un set di URL simile a quello che la route convenzionale predefinita `{controller=Home}/{action=Index}/{id?}` corrisponde.</span><span class="sxs-lookup"><span data-stu-id="26008-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="26008-269">L' `HomeController.Index` azione viene eseguita per uno dei percorsi URL `/` ,, `/Home` `/Home/Index` o `/Home/Index/3` .</span><span class="sxs-lookup"><span data-stu-id="26008-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="26008-270">In questo esempio viene evidenziata una differenza di programmazione principale tra routing degli attributi e [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="26008-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="26008-271">Il routing degli attributi richiede un maggior numero di input per specificare una route.</span><span class="sxs-lookup"><span data-stu-id="26008-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="26008-272">La route predefinita convenzionale gestisce le route in maniera più concisa.</span><span class="sxs-lookup"><span data-stu-id="26008-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="26008-273">Tuttavia, il routing degli attributi consente e richiede un controllo preciso dei modelli di route applicabili a ciascuna [azione](#action).</span><span class="sxs-lookup"><span data-stu-id="26008-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="26008-274">Con il routing degli attributi, i nomi del controller e dell'azione non sono parte dell'azione corrispondente, a meno che non venga utilizzata la [sostituzione del token](#routing-token-replacement-templates-ref-label) .</span><span class="sxs-lookup"><span data-stu-id="26008-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="26008-275">L'esempio seguente corrisponde agli stessi URL dell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="26008-276">Il codice seguente usa la sostituzione dei token per `action` e `controller` :</span><span class="sxs-lookup"><span data-stu-id="26008-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="26008-277">Il codice seguente si applica `[Route("[controller]/[action]")]` al controller:</span><span class="sxs-lookup"><span data-stu-id="26008-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="26008-278">Nel codice precedente, i `Index` modelli di metodo devono anteporre `/` o `~/` ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="26008-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="26008-279">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="26008-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="26008-280">Vedere [precedenza del modello di route](xref:fundamentals/routing#rtp) per informazioni sulla selezione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="26008-281">Nomi riservati di routing</span><span class="sxs-lookup"><span data-stu-id="26008-281">Reserved routing names</span></span>

<span data-ttu-id="26008-282">Quando si usano i controller o le pagine, le parole chiave seguenti sono nomi di parametro di route riservati Razor :</span><span class="sxs-lookup"><span data-stu-id="26008-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="26008-283">`page`L'uso di come parametro di route con routing degli attributi è un errore comune.</span><span class="sxs-lookup"><span data-stu-id="26008-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="26008-284">Questa operazione comporta un comportamento incoerente e confuso con la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="26008-285">I nomi di parametro speciali vengono usati dalla generazione dell'URL per determinare se un'operazione di generazione URL fa riferimento a una Razor pagina o a un controller.</span><span class="sxs-lookup"><span data-stu-id="26008-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="26008-286">Modelli di verbi HTTP</span><span class="sxs-lookup"><span data-stu-id="26008-286">HTTP verb templates</span></span>

<span data-ttu-id="26008-287">ASP.NET Core dispone dei seguenti modelli di verbi HTTP:</span><span class="sxs-lookup"><span data-stu-id="26008-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="26008-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="26008-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="26008-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="26008-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="26008-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="26008-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="26008-294">Modelli di route</span><span class="sxs-lookup"><span data-stu-id="26008-294">Route templates</span></span>

<span data-ttu-id="26008-295">ASP.NET Core include i modelli di route seguenti:</span><span class="sxs-lookup"><span data-stu-id="26008-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="26008-296">Tutti i [modelli di verbi HTTP](#verb) sono modelli di route.</span><span class="sxs-lookup"><span data-stu-id="26008-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="26008-297">[Route](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="26008-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="26008-298">Routing degli attributi con attributi di verbi http</span><span class="sxs-lookup"><span data-stu-id="26008-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="26008-299">Si consideri il controller seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="26008-300">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-300">In the preceding code:</span></span>

* <span data-ttu-id="26008-301">Ogni azione contiene l' `[HttpGet]` attributo, che vincola la corrispondenza solo alle richieste HTTP Get.</span><span class="sxs-lookup"><span data-stu-id="26008-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="26008-302">L' `GetProduct` azione include il `"{id}"` modello, pertanto `id` viene aggiunto al `"api/[controller]"` modello sul controller.</span><span class="sxs-lookup"><span data-stu-id="26008-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="26008-303">Il modello di metodi è `"api/[controller]/"{id}""` .</span><span class="sxs-lookup"><span data-stu-id="26008-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="26008-304">Pertanto questa azione corrisponde solo alle richieste Get di per il form,, e `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` così via.</span><span class="sxs-lookup"><span data-stu-id="26008-304">Therefore this action only matches GET requests of for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="26008-305">L' `GetIntProduct` azione contiene il `"int/{id:int}")` modello.</span><span class="sxs-lookup"><span data-stu-id="26008-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="26008-306">La `:int` parte del modello vincola i `id` valori di route alle stringhe che possono essere convertite in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="26008-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="26008-307">Una richiesta GET a `/api/test2/int/abc` :</span><span class="sxs-lookup"><span data-stu-id="26008-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="26008-308">Non corrisponde a questa azione.</span><span class="sxs-lookup"><span data-stu-id="26008-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="26008-309">Restituisce un errore [404 non trovato](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .</span><span class="sxs-lookup"><span data-stu-id="26008-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="26008-310">L' `GetInt2Product` azione contiene `{id}` nel modello, ma non vincola i `id` valori che possono essere convertiti in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="26008-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="26008-311">Una richiesta GET a `/api/test2/int2/abc` :</span><span class="sxs-lookup"><span data-stu-id="26008-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="26008-312">Corrisponde a questa route.</span><span class="sxs-lookup"><span data-stu-id="26008-312">Matches this route.</span></span>
  * <span data-ttu-id="26008-313">L'associazione di modelli non riesce a eseguire `abc` la conversione in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="26008-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="26008-314">Il `id` parametro del metodo è Integer.</span><span class="sxs-lookup"><span data-stu-id="26008-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="26008-315">Restituisce una [richiesta non valida 400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) perché l'associazione di modelli non è riuscita a eseguire la conversione `abc` in un numero intero.</span><span class="sxs-lookup"><span data-stu-id="26008-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="26008-316">Il routing degli attributi può utilizzare <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributi come <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> e <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> .</span><span class="sxs-lookup"><span data-stu-id="26008-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="26008-317">Tutti gli attributi del [verbo http](#verb) accettano un modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="26008-318">Nell'esempio seguente vengono illustrate due azioni che corrispondono allo stesso modello di route:</span><span class="sxs-lookup"><span data-stu-id="26008-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="26008-319">Utilizzando il percorso URL `/products3` :</span><span class="sxs-lookup"><span data-stu-id="26008-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="26008-320">L' `MyProductsController.ListProducts` azione viene eseguita quando il [verbo http](#verb) è `GET` .</span><span class="sxs-lookup"><span data-stu-id="26008-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="26008-321">L' `MyProductsController.CreateProduct` azione viene eseguita quando il [verbo http](#verb) è `POST` .</span><span class="sxs-lookup"><span data-stu-id="26008-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="26008-322">Quando si compila un'API REST, è raro che sia necessario usare `[Route(...)]` su un metodo di azione perché l'azione accetta tutti i metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="26008-323">È preferibile usare l' [attributo verbo http](#verb) più specifico per essere precisi sul supporto dell'API.</span><span class="sxs-lookup"><span data-stu-id="26008-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="26008-324">I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.</span><span class="sxs-lookup"><span data-stu-id="26008-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="26008-325">Le API REST devono usare il routing degli attributi per modellare la funzionalità dell'app come un set di risorse in cui le operazioni sono rappresentate da verbi HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="26008-326">Ciò significa che molte operazioni, ad esempio GET e POST nella stessa risorsa logica, utilizzano lo stesso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="26008-327">Il routing degli attributi offre un livello di controllo necessario per progettare con attenzione il layout dell'endpoint pubblico di un'API.</span><span class="sxs-lookup"><span data-stu-id="26008-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="26008-328">Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="26008-329">Nell'esempio seguente `id` è necessario come parte del percorso URL:</span><span class="sxs-lookup"><span data-stu-id="26008-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="26008-330">`Products2ApiController.GetProduct(int)`Azione:</span><span class="sxs-lookup"><span data-stu-id="26008-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="26008-331">Viene eseguito con un percorso URL come`/products2/3`</span><span class="sxs-lookup"><span data-stu-id="26008-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="26008-332">Non viene eseguito con il percorso URL `/products2` .</span><span class="sxs-lookup"><span data-stu-id="26008-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="26008-333">L'attributo [[consums]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) consente a un'azione di limitare i tipi di contenuto della richiesta supportati.</span><span class="sxs-lookup"><span data-stu-id="26008-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="26008-334">Per altre informazioni, vedere [definire i tipi di contenuto della richiesta supportati con l'attributo consums](xref:web-api/index#consumes).</span><span class="sxs-lookup"><span data-stu-id="26008-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="26008-335">Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.</span><span class="sxs-lookup"><span data-stu-id="26008-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="26008-336">Per altre informazioni su `[ApiController]` , vedere [attributo ApiController](xref:web-api/index##apicontroller-attribute).</span><span class="sxs-lookup"><span data-stu-id="26008-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="26008-337">Nome route</span><span class="sxs-lookup"><span data-stu-id="26008-337">Route name</span></span>

<span data-ttu-id="26008-338">Nell’esempio di codice riportato di seguito viene definito un nome di route di `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="26008-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="26008-339">I nomi di route possono essere usati per generare un URL in base a un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="26008-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="26008-340">Nomi di route:</span><span class="sxs-lookup"><span data-stu-id="26008-340">Route names:</span></span>

* <span data-ttu-id="26008-341">Non hanno alcun effetto sul comportamento di corrispondenza dell'URL del routing.</span><span class="sxs-lookup"><span data-stu-id="26008-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="26008-342">Vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-342">Are only used for URL generation.</span></span>

<span data-ttu-id="26008-343">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="26008-344">Confrontare il codice precedente con la route predefinita convenzionale, che definisce il `id` parametro come facoltativo ( `{id?}` ).</span><span class="sxs-lookup"><span data-stu-id="26008-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="26008-345">La possibilità di specificare con precisione le API offre vantaggi, ad esempio consentendo `/products` e `/products/5` di essere inviati a diverse azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="26008-346">Combinazione di route per attributi</span><span class="sxs-lookup"><span data-stu-id="26008-346">Combining attribute routes</span></span>

<span data-ttu-id="26008-347">Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="26008-348">I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="26008-349">Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="26008-350">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-350">In the preceding example:</span></span>

* <span data-ttu-id="26008-351">Il percorso URL `/products` può corrispondere`ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="26008-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="26008-352">Il percorso URL `/products/5` può corrispondere `ProductsApi.GetProduct(int)` .</span><span class="sxs-lookup"><span data-stu-id="26008-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="26008-353">Entrambe le azioni corrispondono solo a HTTP `GET` perché sono contrassegnate con l' `[HttpGet]` attributo.</span><span class="sxs-lookup"><span data-stu-id="26008-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="26008-354">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="26008-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="26008-355">Nell'esempio seguente viene individuata la corrispondenza di un set di percorsi URL simile a quello della route predefinita.</span><span class="sxs-lookup"><span data-stu-id="26008-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="26008-356">Nella tabella seguente vengono illustrati gli `[Route]` attributi del codice precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="26008-357">Attributo</span><span class="sxs-lookup"><span data-stu-id="26008-357">Attribute</span></span>               | <span data-ttu-id="26008-358">Combina con`[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="26008-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="26008-359">Definisce il modello di route</span><span class="sxs-lookup"><span data-stu-id="26008-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="26008-360">Sì</span><span class="sxs-lookup"><span data-stu-id="26008-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="26008-361">Sì</span><span class="sxs-lookup"><span data-stu-id="26008-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="26008-362">**No**</span><span class="sxs-lookup"><span data-stu-id="26008-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="26008-363">Sì</span><span class="sxs-lookup"><span data-stu-id="26008-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="26008-364">Ordine Route attributi</span><span class="sxs-lookup"><span data-stu-id="26008-364">Attribute route order</span></span>

<span data-ttu-id="26008-365">Il routing compila un albero e corrisponde a tutti gli endpoint contemporaneamente:</span><span class="sxs-lookup"><span data-stu-id="26008-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="26008-366">Le voci della route si comportano come se fossero posizionate in un ordinamento ideale.</span><span class="sxs-lookup"><span data-stu-id="26008-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="26008-367">Le route più specifiche hanno la possibilità di essere eseguite prima delle route più generali.</span><span class="sxs-lookup"><span data-stu-id="26008-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="26008-368">Ad esempio, una route di attributo come `blog/search/{topic}` è più specifica di una route di attributo come `blog/{*article}` .</span><span class="sxs-lookup"><span data-stu-id="26008-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="26008-369">`blog/search/{topic}`Per impostazione predefinita, la route ha una priorità più elevata perché è più specifica.</span><span class="sxs-lookup"><span data-stu-id="26008-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="26008-370">Utilizzando il [routing convenzionale](#cr), lo sviluppatore è responsabile dell'inserimento delle route nell'ordine desiderato.</span><span class="sxs-lookup"><span data-stu-id="26008-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="26008-371">Le route degli attributi possono configurare un ordine utilizzando la <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> Proprietà.</span><span class="sxs-lookup"><span data-stu-id="26008-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="26008-372">Tutti gli [attributi di route](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) forniti dal Framework includono `Order` .</span><span class="sxs-lookup"><span data-stu-id="26008-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="26008-373">Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="26008-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="26008-374">L'ordine predefinito è `0`.</span><span class="sxs-lookup"><span data-stu-id="26008-374">The default order is `0`.</span></span> <span data-ttu-id="26008-375">L'impostazione di una route tramite `Order = -1` viene eseguita prima delle route che non impostano un ordine.</span><span class="sxs-lookup"><span data-stu-id="26008-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="26008-376">L'impostazione di una route tramite `Order = 1` viene eseguita dopo l'ordinamento predefinito della route.</span><span class="sxs-lookup"><span data-stu-id="26008-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="26008-377">**Evitare** la dipendenza da `Order` .</span><span class="sxs-lookup"><span data-stu-id="26008-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="26008-378">Se lo spazio URL di un'app richiede che i valori di ordine espliciti vengano indirizzati correttamente, è probabile che anche i client siano confusi.</span><span class="sxs-lookup"><span data-stu-id="26008-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="26008-379">In generale, il routing degli attributi seleziona la route corretta con l'URL corrispondente.</span><span class="sxs-lookup"><span data-stu-id="26008-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="26008-380">Se l'ordine predefinito usato per la generazione di URL non funziona, l'uso di un nome di route come override è in genere più semplice dell'applicazione della `Order` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="26008-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="26008-381">Considerare i seguenti due controller che definiscono entrambi la corrispondenza della route `/home` :</span><span class="sxs-lookup"><span data-stu-id="26008-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="26008-382">La richiesta `/home` con il codice precedente genera un'eccezione simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="26008-383">`Order`L'aggiunta a uno degli attributi di route risolve l'ambiguità:</span><span class="sxs-lookup"><span data-stu-id="26008-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="26008-384">Con il codice precedente, `/home` esegue l' `HomeController.Index` endpoint.</span><span class="sxs-lookup"><span data-stu-id="26008-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="26008-385">Per ottenere l'oggetto `MyDemoController.MyIndex` , richiedere `/home/MyIndex` .</span><span class="sxs-lookup"><span data-stu-id="26008-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="26008-386">**Nota**:</span><span class="sxs-lookup"><span data-stu-id="26008-386">**Note**:</span></span>

* <span data-ttu-id="26008-387">Il codice precedente è un esempio o una progettazione di routing insufficiente.</span><span class="sxs-lookup"><span data-stu-id="26008-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="26008-388">È stato usato per illustrare la `Order` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="26008-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="26008-389">La `Order` Proprietà risolve solo l'ambiguità e non è possibile trovare una corrispondenza per il modello.</span><span class="sxs-lookup"><span data-stu-id="26008-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="26008-390">È preferibile rimuovere il `[Route("Home")]` modello.</span><span class="sxs-lookup"><span data-stu-id="26008-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="26008-391">Vedere la pagina relativa alle [ Razor convenzioni di route e app: ordine](xref:razor-pages/razor-pages-conventions#route-order) di route per informazioni sull'ordine di route con Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="26008-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="26008-392">In alcuni casi, viene restituito un errore HTTP 500 con route ambigue.</span><span class="sxs-lookup"><span data-stu-id="26008-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="26008-393">Usare la [registrazione](xref:fundamentals/logging/index) per verificare quali endpoint hanno causato `AmbiguousMatchException` .</span><span class="sxs-lookup"><span data-stu-id="26008-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="26008-394">Sostituzione di token nei modelli di route [controller], [azione], [area]</span><span class="sxs-lookup"><span data-stu-id="26008-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="26008-395">Per praticità, le route degli attributi supportano la sostituzione dei token per i parametri di route riservati, racchiudendo un token in uno dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="26008-395">For convenience, attribute routes support token replacement for reserved route parameters by enclosing a token in one of the following:</span></span>

* <span data-ttu-id="26008-396">Parentesi quadre:`[]`</span><span class="sxs-lookup"><span data-stu-id="26008-396">Square brackets: `[]`</span></span>
* <span data-ttu-id="26008-397">Parentesi graffe:`{}`</span><span class="sxs-lookup"><span data-stu-id="26008-397">Curly braces: `{}`</span></span>

<span data-ttu-id="26008-398">I token `[action]` , `[area]` e `[controller]` vengono sostituiti con i valori del nome dell'azione, del nome dell'area e del nome del controller dall'azione in cui è definita la route:</span><span class="sxs-lookup"><span data-stu-id="26008-398">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="26008-399">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-399">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="26008-400">Corrisponde`/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="26008-400">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="26008-401">Corrisponde`/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="26008-401">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="26008-402">La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-402">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="26008-403">L'esempio precedente si comporta come il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-403">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="26008-404">Le route con attributi possono anche essere combinate con l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="26008-404">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="26008-405">Questa funzionalità è potente in combinazione con la sostituzione dei token.</span><span class="sxs-lookup"><span data-stu-id="26008-405">This is powerful combined with token replacement.</span></span> <span data-ttu-id="26008-406">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-406">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="26008-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`genera un nome di route univoco per ogni azione:</span><span class="sxs-lookup"><span data-stu-id="26008-407">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="26008-408">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-408">Token replacement also applies to route names defined by attribute routes.</span></span>
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
<span data-ttu-id="26008-409"> genera un nome di route univoco per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="26008-409">generates a unique route name for each action.</span></span>

<span data-ttu-id="26008-410">Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).</span><span class="sxs-lookup"><span data-stu-id="26008-410">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="26008-411">Usare un trasformatore di parametri per personalizzare la sostituzione dei token</span><span class="sxs-lookup"><span data-stu-id="26008-411">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="26008-412">La sostituzione dei token può essere personalizzata usando un trasformatore di parametri.</span><span class="sxs-lookup"><span data-stu-id="26008-412">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="26008-413">Un trasformatore di parametri implementa <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> e trasforma il valore dei parametri.</span><span class="sxs-lookup"><span data-stu-id="26008-413">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="26008-414">Un trasformatore di parametro personalizzato, ad esempio, `SlugifyParameterTransformer` modifica il `SubscriptionManagement` valore di route in `subscription-management` :</span><span class="sxs-lookup"><span data-stu-id="26008-414">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="26008-415"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> è una convenzione del modello di applicazione che:</span><span class="sxs-lookup"><span data-stu-id="26008-415">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="26008-416">Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-416">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="26008-417">Personalizza i valori dei token delle route di attributi quando vengono sostituiti.</span><span class="sxs-lookup"><span data-stu-id="26008-417">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="26008-418">Il `ListAll` metodo precedente corrisponde a `/subscription-management/list-all` .</span><span class="sxs-lookup"><span data-stu-id="26008-418">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="26008-419">La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="26008-419">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="26008-420">Per la definizione di Slug, vedere la [documentazione Web MDN in slug](https://developer.mozilla.org/docs/Glossary/Slug) .</span><span class="sxs-lookup"><span data-stu-id="26008-420">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="26008-421">Route di più attributi</span><span class="sxs-lookup"><span data-stu-id="26008-421">Multiple attribute routes</span></span>

<span data-ttu-id="26008-422">Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione.</span><span class="sxs-lookup"><span data-stu-id="26008-422">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="26008-423">L'uso più comune è simulare il comportamento della route convenzionale predefinita come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-423">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="26008-424">L'inserimento di più attributi di route sul controller significa che ognuno di essi si combina con ognuno degli attributi di route nei metodi di azione:</span><span class="sxs-lookup"><span data-stu-id="26008-424">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="26008-425">Tutti i vincoli di route [http verb](#verb) implementano `IActionConstraint` .</span><span class="sxs-lookup"><span data-stu-id="26008-425">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="26008-426">Quando più attributi di route che implementano <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> sono posizionati in un'azione:</span><span class="sxs-lookup"><span data-stu-id="26008-426">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="26008-427">Ogni vincolo di azione viene combinato con il modello di route applicato al controller.</span><span class="sxs-lookup"><span data-stu-id="26008-427">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="26008-428">L'uso di più route sulle azioni potrebbe sembrare utile e potente, ma è preferibile tenere lo spazio URL dell'app di base e ben definito.</span><span class="sxs-lookup"><span data-stu-id="26008-428">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="26008-429">Usare più route sulle azioni **solo** se necessario, ad esempio per supportare i client esistenti.</span><span class="sxs-lookup"><span data-stu-id="26008-429">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="26008-430">Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi</span><span class="sxs-lookup"><span data-stu-id="26008-430">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="26008-431">Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.</span><span class="sxs-lookup"><span data-stu-id="26008-431">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="26008-432">Nel codice precedente, `[HttpPost("product/{id:int}")]` applica un vincolo di route.</span><span class="sxs-lookup"><span data-stu-id="26008-432">In the preceding code, `[HttpPost("product/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="26008-433">L' `ProductsController.ShowProduct` azione corrisponde solo ai percorsi URL, ad esempio `/product/3` .</span><span class="sxs-lookup"><span data-stu-id="26008-433">The `ProductsController.ShowProduct` action is matched only by URL paths like `/product/3`.</span></span> <span data-ttu-id="26008-434">La parte del modello `{id:int}` di route vincola tale segmento ai soli numeri interi.</span><span class="sxs-lookup"><span data-stu-id="26008-434">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="26008-435">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-435">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="26008-436">Attributi di Route personalizzati con IRouteTemplateProvider</span><span class="sxs-lookup"><span data-stu-id="26008-436">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="26008-437">Tutti gli [attributi di route](#rt) implementano <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> .</span><span class="sxs-lookup"><span data-stu-id="26008-437">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="26008-438">Il runtime di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="26008-438">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="26008-439">Cerca gli attributi nelle classi controller e nei metodi di azione all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="26008-439">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="26008-440">USA gli attributi che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.</span><span class="sxs-lookup"><span data-stu-id="26008-440">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="26008-441">Implementare `IRouteTemplateProvider` per definire gli attributi di Route personalizzati.</span><span class="sxs-lookup"><span data-stu-id="26008-441">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="26008-442">Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:</span><span class="sxs-lookup"><span data-stu-id="26008-442">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="26008-443">Il `Get` metodo precedente restituisce `Order = 2, Template = api/MyTestApi` .</span><span class="sxs-lookup"><span data-stu-id="26008-443">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="26008-444">Usare il modello di applicazione per personalizzare le route degli attributi</span><span class="sxs-lookup"><span data-stu-id="26008-444">Use application model to customize attribute routes</span></span>

<span data-ttu-id="26008-445">Modello di applicazione:</span><span class="sxs-lookup"><span data-stu-id="26008-445">The application model:</span></span>

* <span data-ttu-id="26008-446">È un modello a oggetti creato all'avvio.</span><span class="sxs-lookup"><span data-stu-id="26008-446">Is an object model created at startup.</span></span>
* <span data-ttu-id="26008-447">Contiene tutti i metadati usati da ASP.NET Core per indirizzare ed eseguire le azioni in un'app.</span><span class="sxs-lookup"><span data-stu-id="26008-447">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="26008-448">Il modello di applicazione include tutti i dati raccolti dagli attributi di route.</span><span class="sxs-lookup"><span data-stu-id="26008-448">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="26008-449">I dati degli attributi della route vengono forniti dall' `IRouteTemplateProvider` implementazione di.</span><span class="sxs-lookup"><span data-stu-id="26008-449">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="26008-450">Convenzioni</span><span class="sxs-lookup"><span data-stu-id="26008-450">Conventions:</span></span>

* <span data-ttu-id="26008-451">È possibile scrivere per modificare il modello di applicazione per personalizzare il comportamento del routing.</span><span class="sxs-lookup"><span data-stu-id="26008-451">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="26008-452">Vengono letti all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="26008-452">Are read at app startup.</span></span>

<span data-ttu-id="26008-453">Questa sezione illustra un esempio di base di personalizzazione del routing con il modello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-453">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="26008-454">Il codice seguente rende le route approssimativamente allineate con la struttura di cartelle del progetto.</span><span class="sxs-lookup"><span data-stu-id="26008-454">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="26008-455">Il codice seguente impedisce `namespace` che la Convenzione venga applicata ai controller indirizzati con l'attributo:</span><span class="sxs-lookup"><span data-stu-id="26008-455">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="26008-456">Ad esempio, il controller seguente non usa `NamespaceRoutingConvention` :</span><span class="sxs-lookup"><span data-stu-id="26008-456">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="26008-457">Il metodo `NamespaceRoutingConvention.Apply`:</span><span class="sxs-lookup"><span data-stu-id="26008-457">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="26008-458">Non esegue alcuna operazione se il controller è instradato all'attributo.</span><span class="sxs-lookup"><span data-stu-id="26008-458">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="26008-459">Imposta il modello di controller basato su `namespace` , con la base `namespace` rimossa.</span><span class="sxs-lookup"><span data-stu-id="26008-459">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="26008-460">Il `NamespaceRoutingConvention` può essere applicato in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="26008-460">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="26008-461">Si consideri, ad esempio, il controller seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-461">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="26008-462">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="26008-462">In the preceding code:</span></span>

* <span data-ttu-id="26008-463">La base `namespace` è `My.Application` .</span><span class="sxs-lookup"><span data-stu-id="26008-463">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="26008-464">Il nome completo del controller precedente è `My.Application.Admin.Controllers.UsersController` .</span><span class="sxs-lookup"><span data-stu-id="26008-464">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="26008-465">`NamespaceRoutingConvention`Imposta il modello dei controller su `Admin/Controllers/Users/[action]/{id?` .</span><span class="sxs-lookup"><span data-stu-id="26008-465">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="26008-466">Il `NamespaceRoutingConvention` può essere applicato anche come attributo in un controller:</span><span class="sxs-lookup"><span data-stu-id="26008-466">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="26008-467">Routing misto: routing con attributi e routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-467">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="26008-468">ASP.NET Core app possono combinare l'uso del routing convenzionale e del routing degli attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-468">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="26008-469">In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.</span><span class="sxs-lookup"><span data-stu-id="26008-469">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="26008-470">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-470">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="26008-471">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-471">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="26008-472">Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa.</span><span class="sxs-lookup"><span data-stu-id="26008-472">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="26008-473">**Qualsiasi** attributo di route nel controller esegue il routing di **tutte** le azioni nell'attributo controller.</span><span class="sxs-lookup"><span data-stu-id="26008-473">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="26008-474">Il routing degli attributi e il routing convenzionale utilizzano lo stesso motore di routing.</span><span class="sxs-lookup"><span data-stu-id="26008-474">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="26008-475">Generazione di URL e valori di ambiente</span><span class="sxs-lookup"><span data-stu-id="26008-475">URL Generation and ambient values</span></span>

<span data-ttu-id="26008-476">Le app possono usare le funzionalità di generazione URL di routing per generare collegamenti URL alle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-476">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="26008-477">La generazione di URL Elimina gli URL hardcoded, rendendo il codice più affidabile e gestibile.</span><span class="sxs-lookup"><span data-stu-id="26008-477">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="26008-478">Questa sezione è incentrata sulle funzionalità di generazione degli URL fornite da MVC e copre solo le nozioni di base sul funzionamento della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-478">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="26008-479">Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-479">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="26008-480">L' <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interfaccia è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-480">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="26008-481">Un'istanza di `IUrlHelper` è disponibile tramite la `Url` Proprietà in controller, visualizzazioni e componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="26008-481">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="26008-482">Nell'esempio seguente l' `IUrlHelper` interfaccia viene utilizzata tramite la `Controller.Url` proprietà per generare un URL a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="26008-482">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="26008-483">Se l'app usa la route convenzionale predefinita, il valore della `url` variabile è la stringa del percorso dell'URL `/UrlGeneration/Destination` .</span><span class="sxs-lookup"><span data-stu-id="26008-483">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="26008-484">Questo percorso URL viene creato tramite il routing combinando:</span><span class="sxs-lookup"><span data-stu-id="26008-484">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="26008-485">Valori della route dalla richiesta corrente, denominati **valori di ambiente**.</span><span class="sxs-lookup"><span data-stu-id="26008-485">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="26008-486">I valori passati a `Url.Action` e sostituiscono tali valori nel modello di route:</span><span class="sxs-lookup"><span data-stu-id="26008-486">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="26008-487">Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente.</span><span class="sxs-lookup"><span data-stu-id="26008-487">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="26008-488">Un parametro di route che non ha un valore può:</span><span class="sxs-lookup"><span data-stu-id="26008-488">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="26008-489">Utilizzare un valore predefinito se ne è presente uno.</span><span class="sxs-lookup"><span data-stu-id="26008-489">Use a default value if it has one.</span></span>
* <span data-ttu-id="26008-490">Viene ignorato se è facoltativo.</span><span class="sxs-lookup"><span data-stu-id="26008-490">Be skipped if it's optional.</span></span> <span data-ttu-id="26008-491">Ad esempio, `id` dal modello di route `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="26008-491">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="26008-492">La generazione di URL non riesce se un parametro di route obbligatorio non ha un valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="26008-492">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="26008-493">Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="26008-493">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="26008-494">Nell'esempio precedente di viene `Url.Action` presupposto il [routing convenzionale](#cr).</span><span class="sxs-lookup"><span data-stu-id="26008-494">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="26008-495">La generazione di URL funziona in modo analogo al [routing degli attributi](#ar), sebbene i concetti siano diversi.</span><span class="sxs-lookup"><span data-stu-id="26008-495">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="26008-496">Con routing convenzionale:</span><span class="sxs-lookup"><span data-stu-id="26008-496">With conventional routing:</span></span>

* <span data-ttu-id="26008-497">I valori di route vengono usati per espandere un modello.</span><span class="sxs-lookup"><span data-stu-id="26008-497">The route values are used to expand a template.</span></span>
* <span data-ttu-id="26008-498">I valori di route per `controller` e `action` vengono in genere visualizzati in tale modello.</span><span class="sxs-lookup"><span data-stu-id="26008-498">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="26008-499">Questa operazione funziona perché gli URL corrispondenti al routing rispettano una convenzione.</span><span class="sxs-lookup"><span data-stu-id="26008-499">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="26008-500">Nell'esempio seguente viene usato il routing degli attributi:</span><span class="sxs-lookup"><span data-stu-id="26008-500">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="26008-501">L' `Source` azione nel codice precedente genera `custom/url/to/destination` .</span><span class="sxs-lookup"><span data-stu-id="26008-501">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="26008-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator>è stato aggiunto in ASP.NET Core 3,0 come alternativa a `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="26008-502"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="26008-503">`LinkGenerator`offre funzionalità simili ma più flessibili.</span><span class="sxs-lookup"><span data-stu-id="26008-503">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="26008-504">Ogni metodo in `IUrlHelper` dispone anche di una famiglia di metodi corrispondente `LinkGenerator` .</span><span class="sxs-lookup"><span data-stu-id="26008-504">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="26008-505">Generazione di URL in base al nome dell'azione</span><span class="sxs-lookup"><span data-stu-id="26008-505">Generating URLs by action name</span></span>

<span data-ttu-id="26008-506">[URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)e tutti gli overload correlati sono progettati per generare l'endpoint di destinazione specificando il nome del controller e il nome dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-506">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="26008-507">Quando `Url.Action` si usa, i valori di route correnti per `controller` e `action` vengono forniti dal runtime:</span><span class="sxs-lookup"><span data-stu-id="26008-507">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="26008-508">Il valore di `controller` e `action` fa parte dei [valori di ambiente](#ambient) e.</span><span class="sxs-lookup"><span data-stu-id="26008-508">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="26008-509">Il metodo `Url.Action` Usa sempre i valori correnti di `action` e `controller` e genera un percorso URL che indirizza all'azione corrente.</span><span class="sxs-lookup"><span data-stu-id="26008-509">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="26008-510">Il routing tenta di usare i valori nei valori di ambiente per inserire le informazioni non fornite durante la generazione di un URL.</span><span class="sxs-lookup"><span data-stu-id="26008-510">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="26008-511">Si consideri una route come `{a}/{b}/{c}/{d}` con i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }` :</span><span class="sxs-lookup"><span data-stu-id="26008-511">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="26008-512">Il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="26008-512">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="26008-513">Il routing ha informazioni sufficienti perché tutti i parametri di route hanno un valore.</span><span class="sxs-lookup"><span data-stu-id="26008-513">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="26008-514">Se il valore `{ d = Donovan }` viene aggiunto:</span><span class="sxs-lookup"><span data-stu-id="26008-514">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="26008-515">Il valore `{ d = David }` viene ignorato.</span><span class="sxs-lookup"><span data-stu-id="26008-515">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="26008-516">Il percorso dell'URL generato è `Alice/Bob/Carol/Donovan` .</span><span class="sxs-lookup"><span data-stu-id="26008-516">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="26008-517">**Avviso**: i percorsi URL sono gerarchici.</span><span class="sxs-lookup"><span data-stu-id="26008-517">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="26008-518">Nell'esempio precedente, se il valore `{ c = Cheryl }` viene aggiunto:</span><span class="sxs-lookup"><span data-stu-id="26008-518">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="26008-519">Entrambi i valori `{ c = Carol, d = David }` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="26008-519">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="26008-520">Non esiste più un valore per `d` e la generazione dell'URL ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="26008-520">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="26008-521">`c` `d` Per generare un URL, è necessario specificare i valori desiderati di e.</span><span class="sxs-lookup"><span data-stu-id="26008-521">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="26008-522">È possibile che si verifichi questo problema con la route predefinita `{controller}/{action}/{id?}` .</span><span class="sxs-lookup"><span data-stu-id="26008-522">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="26008-523">Questo problema è raro in pratica perché `Url.Action` specifica sempre in modo esplicito un `controller` `action` valore e.</span><span class="sxs-lookup"><span data-stu-id="26008-523">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="26008-524">Diversi overload di [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) accettano un oggetto dei valori di route per fornire valori per i parametri di route diversi da `controller` e `action` .</span><span class="sxs-lookup"><span data-stu-id="26008-524">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="26008-525">L'oggetto valori di route viene spesso usato con `id` .</span><span class="sxs-lookup"><span data-stu-id="26008-525">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="26008-526">Ad esempio, `Url.Action("Buy", "Products", new { id = 17 })`</span><span class="sxs-lookup"><span data-stu-id="26008-526">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="26008-527">Oggetto valori di route:</span><span class="sxs-lookup"><span data-stu-id="26008-527">The route values object:</span></span>

* <span data-ttu-id="26008-528">Per convenzione è in genere un oggetto di tipo anonimo.</span><span class="sxs-lookup"><span data-stu-id="26008-528">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="26008-529">Può essere un oggetto `IDictionary<>` o un oggetto [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span><span class="sxs-lookup"><span data-stu-id="26008-529">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="26008-530">I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="26008-530">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="26008-531">Il codice precedente genera `/Products/Buy/17?color=red` .</span><span class="sxs-lookup"><span data-stu-id="26008-531">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="26008-532">Il codice seguente genera un URL assoluto:</span><span class="sxs-lookup"><span data-stu-id="26008-532">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="26008-533">Per creare un URL assoluto, usare uno dei seguenti elementi:</span><span class="sxs-lookup"><span data-stu-id="26008-533">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="26008-534">Overload che accetta un oggetto `protocol` .</span><span class="sxs-lookup"><span data-stu-id="26008-534">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="26008-535">Ad esempio, il codice precedente.</span><span class="sxs-lookup"><span data-stu-id="26008-535">For example, the preceding code.</span></span>
* <span data-ttu-id="26008-536">[LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), che genera URI assoluti per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="26008-536">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="26008-537">Genera URL per Route</span><span class="sxs-lookup"><span data-stu-id="26008-537">Generate URLs by route</span></span>

<span data-ttu-id="26008-538">Nel codice precedente è stata illustrata la generazione di un URL passando il nome del controller e dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-538">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="26008-539">`IUrlHelper`fornisce anche la famiglia di metodi [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) .</span><span class="sxs-lookup"><span data-stu-id="26008-539">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="26008-540">Questi metodi sono simili a [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ma non copiano i valori correnti di `action` e `controller` nei valori di route.</span><span class="sxs-lookup"><span data-stu-id="26008-540">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="26008-541">L'utilizzo più comune di `Url.RouteUrl` :</span><span class="sxs-lookup"><span data-stu-id="26008-541">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="26008-542">Specifica un nome di route per generare l'URL.</span><span class="sxs-lookup"><span data-stu-id="26008-542">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="26008-543">In genere non specifica un nome di controller o di azione.</span><span class="sxs-lookup"><span data-stu-id="26008-543">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="26008-544">Il Razor file seguente genera un collegamento HTML a `Destination_Route` :</span><span class="sxs-lookup"><span data-stu-id="26008-544">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a><span data-ttu-id="26008-545">Genera URL in HTML eRazor</span><span class="sxs-lookup"><span data-stu-id="26008-545">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="26008-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>fornisce i <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metodi [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) e [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) per generare `<form>` `<a>` rispettivamente gli elementi e.</span><span class="sxs-lookup"><span data-stu-id="26008-546"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="26008-547">Questi metodi usano il metodo [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) per generare un URL e accettano argomenti simili.</span><span class="sxs-lookup"><span data-stu-id="26008-547">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="26008-548">Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="26008-548">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="26008-549">Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`.</span><span class="sxs-lookup"><span data-stu-id="26008-549">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="26008-550">Entrambi usano `IUrlHelper` per la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="26008-550">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="26008-551">Per ulteriori informazioni, vedere [Helper tag nei moduli](xref:mvc/views/working-with-forms) .</span><span class="sxs-lookup"><span data-stu-id="26008-551">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="26008-552">All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="26008-552">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="26008-553">Generazione dell'URL nei risultati dell'azione</span><span class="sxs-lookup"><span data-stu-id="26008-553">URL generation in Action Results</span></span>

<span data-ttu-id="26008-554">Negli esempi precedenti è stato illustrato l'utilizzo `IUrlHelper` di in un controller.</span><span class="sxs-lookup"><span data-stu-id="26008-554">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="26008-555">L'utilizzo più comune in un controller consiste nel generare un URL come parte del risultato di un'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-555">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="26008-556">Le classi di base <xref:Microsoft.AspNetCore.Mvc.ControllerBase> e <xref:Microsoft.AspNetCore.Mvc.Controller> offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="26008-556">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="26008-557">Un utilizzo tipico prevede il reindirizzamento dopo l'accettazione dell'input utente:</span><span class="sxs-lookup"><span data-stu-id="26008-557">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="26008-558">I metodi factory dei risultati delle azioni come <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> e <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> seguono un modello simile ai metodi in `IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="26008-558">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="26008-559">Caso speciale per le route convenzionali dedicate</span><span class="sxs-lookup"><span data-stu-id="26008-559">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="26008-560">Il [routing convenzionale](#cr) può usare un tipo speciale di definizione di route denominata [Route convenzionale dedicata](#dcr).</span><span class="sxs-lookup"><span data-stu-id="26008-560">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="26008-561">Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata:</span><span class="sxs-lookup"><span data-stu-id="26008-561">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="26008-562">Usando le definizioni di route precedenti, `Url.Action("Index", "Home")` genera il percorso URL `/` usando la `default` Route, ma perché?</span><span class="sxs-lookup"><span data-stu-id="26008-562">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="26008-563">Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="26008-563">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="26008-564">Le [Route convenzionali dedicate](#dcr) si basano su un comportamento speciale dei valori predefiniti che non hanno un parametro di route corrispondente che impedisce che la route sia troppo [greedy](xref:fundamentals/routing#greedy) con la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-564">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="26008-565">In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route.</span><span class="sxs-lookup"><span data-stu-id="26008-565">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="26008-566">Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="26008-566">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="26008-567">La generazione di URL con ha `blog` esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono `{ controller = Blog, action = Article }` .</span><span class="sxs-lookup"><span data-stu-id="26008-567">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="26008-568">Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="26008-568">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="26008-569">Aree</span><span class="sxs-lookup"><span data-stu-id="26008-569">Areas</span></span>

<span data-ttu-id="26008-570">Le [aree](xref:mvc/controllers/areas) sono una funzionalità di MVC utilizzata per organizzare le funzionalità correlate in un gruppo come separato:</span><span class="sxs-lookup"><span data-stu-id="26008-570">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="26008-571">Spazio dei nomi di routing per le azioni del controller.</span><span class="sxs-lookup"><span data-stu-id="26008-571">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="26008-572">Struttura di cartelle per le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="26008-572">Folder structure for views.</span></span>

<span data-ttu-id="26008-573">L'uso delle aree consente a un'app di avere più controller con lo stesso nome, purché abbiano aree diverse.</span><span class="sxs-lookup"><span data-stu-id="26008-573">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="26008-574">Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="26008-574">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="26008-575">Questa sezione illustra il modo in cui il routing interagisce con le aree.</span><span class="sxs-lookup"><span data-stu-id="26008-575">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="26008-576">Per informazioni dettagliate sull'uso delle aree con le visualizzazioni, vedere [aree](xref:mvc/controllers/areas) .</span><span class="sxs-lookup"><span data-stu-id="26008-576">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="26008-577">Nell'esempio seguente viene configurato MVC per l'utilizzo della route convenzionale predefinita e di una `area` Route per un oggetto `area` denominato `Blog` :</span><span class="sxs-lookup"><span data-stu-id="26008-577">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="26008-578">Nel codice precedente, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> viene chiamato per creare l'oggetto `"blog_route"` .</span><span class="sxs-lookup"><span data-stu-id="26008-578">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="26008-579">Il secondo parametro, `"Blog"` , è il nome dell'area.</span><span class="sxs-lookup"><span data-stu-id="26008-579">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="26008-580">Quando si corrisponde a un percorso URL come `/Manage/Users/AddUser` , la `"blog_route"` Route genera i valori della route `{ area = Blog, controller = Users, action = AddUser }` .</span><span class="sxs-lookup"><span data-stu-id="26008-580">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="26008-581">Il `area` valore di route viene prodotto da un valore predefinito per `area` .</span><span class="sxs-lookup"><span data-stu-id="26008-581">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="26008-582">La route creata da `MapAreaControllerRoute` equivale a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="26008-582">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="26008-583">`MapAreaControllerRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`.</span><span class="sxs-lookup"><span data-stu-id="26008-583">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="26008-584">Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-584">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="26008-585">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="26008-585">Conventional routing is order-dependent.</span></span> <span data-ttu-id="26008-586">In generale, le route con aree devono essere posizionate in precedenza perché sono più specifiche delle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="26008-586">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="26008-587">Utilizzando l'esempio precedente, i valori della route `{ area = Blog, controller = Users, action = AddUser }` corrispondono all'azione seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-587">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="26008-588">L'attributo [[area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) è quello che denota un controller come parte di un'area.</span><span class="sxs-lookup"><span data-stu-id="26008-588">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="26008-589">Questo controller si trova nell' `Blog` area.</span><span class="sxs-lookup"><span data-stu-id="26008-589">This controller is in the `Blog` area.</span></span> <span data-ttu-id="26008-590">I controller senza `[Area]` attributo non sono membri di nessuna area e non corrispondono **not** quando il `area` valore di route viene fornito dal routing.</span><span class="sxs-lookup"><span data-stu-id="26008-590">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="26008-591">Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="26008-591">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="26008-592">Lo spazio dei nomi di ogni controller viene visualizzato qui per completezza.</span><span class="sxs-lookup"><span data-stu-id="26008-592">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="26008-593">Se il controller precedente usa lo stesso spazio dei nomi, verrà generato un errore del compilatore.</span><span class="sxs-lookup"><span data-stu-id="26008-593">If the preceding controllers uses the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="26008-594">Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.</span><span class="sxs-lookup"><span data-stu-id="26008-594">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="26008-595">I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`.</span><span class="sxs-lookup"><span data-stu-id="26008-595">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="26008-596">Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.</span><span class="sxs-lookup"><span data-stu-id="26008-596">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="26008-597">In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="26008-597">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="26008-598">Quando si esegue un'azione all'interno di un'area, il valore di route per `area` è disponibile come [valore di ambiente](#ambient) per il routing da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-598">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="26008-599">Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="26008-599">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="26008-600">Il codice seguente genera un URL per `/Zebra/Users/AddUser` :</span><span class="sxs-lookup"><span data-stu-id="26008-600">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="26008-601">Definizione di azione</span><span class="sxs-lookup"><span data-stu-id="26008-601">Action definition</span></span>

<span data-ttu-id="26008-602">I metodi pubblici in un controller, ad eccezione di quelli con l'attributo [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , sono azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-602">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="26008-603">Codice di esempio</span><span class="sxs-lookup"><span data-stu-id="26008-603">Sample code</span></span>

 * <span data-ttu-id="26008-604">Il metodo [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) è incluso nel [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) e viene usato per visualizzare le informazioni di routing.</span><span class="sxs-lookup"><span data-stu-id="26008-604">The [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) method is included in the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) and is used to display routing information.</span></span>
* <span data-ttu-id="26008-605">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26008-605">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26008-606">ASP.NET Core MVC usa [middleware](xref:fundamentals/middleware/index) di routing per verificare la corrispondenza degli URL delle richieste in ingresso ed eseguirne il mapping alle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-606">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="26008-607">Le route sono definite nel codice di avvio o negli attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-607">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="26008-608">Le route descrivono il modo in cui i percorsi URL devono corrispondere alle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-608">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="26008-609">Vengono usate anche per generare gli URL (per i collegamenti) inviati nelle risposte.</span><span class="sxs-lookup"><span data-stu-id="26008-609">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="26008-610">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-610">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="26008-611">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-611">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="26008-612">Per altre informazioni, vedere [Routing misto](#routing-mixed-ref-label).</span><span class="sxs-lookup"><span data-stu-id="26008-612">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="26008-613">Questo documento illustra le interazioni tra MVC e il routing e il modo in cui le tipiche app MVC usano le funzionalità di routing.</span><span class="sxs-lookup"><span data-stu-id="26008-613">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="26008-614">Vedere [Routing](xref:fundamentals/routing) per informazioni dettagliate sul routing avanzato.</span><span class="sxs-lookup"><span data-stu-id="26008-614">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="26008-615">Impostazione del middleware di routing</span><span class="sxs-lookup"><span data-stu-id="26008-615">Setting up Routing Middleware</span></span>

<span data-ttu-id="26008-616">Nel metodo *Configure* può apparire codice simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-616">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="26008-617">Nella chiamata a `UseMvc`, si usa `MapRoute` per creare una singola route, a cui si farà riferimento come alla route predefinita (`default`).</span><span class="sxs-lookup"><span data-stu-id="26008-617">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="26008-618">La maggior parte delle app MVC userà una route con un modello simile alla route `default`.</span><span class="sxs-lookup"><span data-stu-id="26008-618">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="26008-619">Il modello di route `"{controller=Home}/{action=Index}/{id?}"` può corrispondere a un percorso URL come `/Products/Details/5` ed estrae i valori di route `{ controller = Products, action = Details, id = 5 }` suddividendo il percorso in token.</span><span class="sxs-lookup"><span data-stu-id="26008-619">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="26008-620">MVC tenterà di individuare un controller denominato `ProductsController` e di eseguire l'azione `Details`:</span><span class="sxs-lookup"><span data-stu-id="26008-620">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="26008-621">Si noti che in questo esempio l'associazione di modelli usa il valore di `id = 5` per impostare il parametro `id` su `5` quando si richiama l'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-621">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="26008-622">Per maggiori dettagli, vedere [Associazione di modelli](../models/model-binding.md).</span><span class="sxs-lookup"><span data-stu-id="26008-622">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="26008-623">Usando la route `default`:</span><span class="sxs-lookup"><span data-stu-id="26008-623">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="26008-624">Il modello di route:</span><span class="sxs-lookup"><span data-stu-id="26008-624">The route template:</span></span>

* <span data-ttu-id="26008-625">`{controller=Home}` definisce `Home` come oggetto `controller` predefinito</span><span class="sxs-lookup"><span data-stu-id="26008-625">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="26008-626">`{action=Index}` definisce `Index` come oggetto `action` predefinito</span><span class="sxs-lookup"><span data-stu-id="26008-626">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="26008-627">`{id?}` definisce `id` come facoltativo</span><span class="sxs-lookup"><span data-stu-id="26008-627">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="26008-628">I parametri di route predefiniti e facoltativi non devono necessariamente essere presenti nel percorso URL per trovare una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="26008-628">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="26008-629">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-629">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="26008-630">`"{controller=Home}/{action=Index}/{id?}"` può corrispondere al percorso URL `/` e restituirà i valori di route `{ controller = Home, action = Index }`.</span><span class="sxs-lookup"><span data-stu-id="26008-630">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="26008-631">I valori per `controller` e `action` usano i valori predefiniti, `id` non genera un valore perché non esiste un segmento corrispondente nel percorso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-631">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="26008-632">MVC usa questi valori di route per selezionare l'azione `HomeController` e `Index`:</span><span class="sxs-lookup"><span data-stu-id="26008-632">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="26008-633">Usando questa definizione del controller e il modello di route, l'azione `HomeController.Index` viene eseguita per tutti i percorsi URL seguenti:</span><span class="sxs-lookup"><span data-stu-id="26008-633">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="26008-634">Il metodo pratico `UseMvcWithDefaultRoute`:</span><span class="sxs-lookup"><span data-stu-id="26008-634">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="26008-635">Può essere usato per sostituire:</span><span class="sxs-lookup"><span data-stu-id="26008-635">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="26008-636">`UseMvc` e `UseMvcWithDefaultRoute` aggiungono un'istanza di `RouterMiddleware` alla pipeline middleware.</span><span class="sxs-lookup"><span data-stu-id="26008-636">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="26008-637">MVC non interagisce direttamente con il middleware e usa il routing per gestire le richieste.</span><span class="sxs-lookup"><span data-stu-id="26008-637">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="26008-638">MVC è connesso alle route attraverso un'istanza di `MvcRouteHandler`.</span><span class="sxs-lookup"><span data-stu-id="26008-638">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="26008-639">Il codice all'interno di `UseMvc` è simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-639">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="26008-640">`UseMvc` non definisce direttamente le route, aggiunge un segnaposto alla raccolta di route per la route `attribute`.</span><span class="sxs-lookup"><span data-stu-id="26008-640">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="26008-641">L'overload `UseMvc(Action<IRouteBuilder>)` consente di aggiungere le proprie route e supporta anche il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-641">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="26008-642">`UseMvc`e tutte le relative varianti aggiungono un segnaposto per l'attributo Route: il routing degli attributi è sempre disponibile indipendentemente dalla configurazione `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="26008-642">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="26008-643">`UseMvcWithDefaultRoute` definisce una route predefinita e supporta il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-643">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="26008-644">La sezione [Routing con attributi](#attribute-routing-ref-label) include maggiori dettagli sul routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-644">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="26008-645">Routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-645">Conventional routing</span></span>

<span data-ttu-id="26008-646">La route predefinita (`default`):</span><span class="sxs-lookup"><span data-stu-id="26008-646">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="26008-647">Il codice precedente è un esempio di routing convenzionale.</span><span class="sxs-lookup"><span data-stu-id="26008-647">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="26008-648">Questo stile è denominato routing convenzionale perché stabilisce una *convenzione* per i percorsi URL:</span><span class="sxs-lookup"><span data-stu-id="26008-648">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="26008-649">Il primo segmento di percorso viene mappato al nome del controller.</span><span class="sxs-lookup"><span data-stu-id="26008-649">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="26008-650">Il secondo esegue il mapping al nome dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-650">The second maps to the action name.</span></span>
* <span data-ttu-id="26008-651">Il terzo segmento viene usato per un oggetto facoltativo `id` .</span><span class="sxs-lookup"><span data-stu-id="26008-651">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="26008-652">`id`esegue il mapping a un'entità del modello.</span><span class="sxs-lookup"><span data-stu-id="26008-652">`id` maps to a model entity.</span></span>

<span data-ttu-id="26008-653">Usando la route `default`, il percorso URL `/Products/List` esegue il mapping all'azione `ProductsController.List` e `/Blog/Article/17` lo esegue a `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="26008-653">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="26008-654">Questo mapping si basa **solo** sui nomi dell'azione e del controller e non su spazi dei nomi, percorsi dei file di origine o parametri del metodo.</span><span class="sxs-lookup"><span data-stu-id="26008-654">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="26008-655">L'uso del routing convenzionale con la route predefinita consente di compilare rapidamente l'applicazione senza dover creare un nuovo modello di URL per ogni azione che si definisce.</span><span class="sxs-lookup"><span data-stu-id="26008-655">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="26008-656">Per un'applicazione con azioni di stile CRUD, la coerenza degli URL tra i controller può semplificare il codice e rendere l'interfaccia utente più prevedibile.</span><span class="sxs-lookup"><span data-stu-id="26008-656">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="26008-657">L'oggetto `id` è definito come facoltativo nel modello di route, ovvero le azioni possono essere eseguite senza l'ID specificato come parte dell'URL.</span><span class="sxs-lookup"><span data-stu-id="26008-657">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="26008-658">In genere, se si omette `id` dall'URL, il valore viene impostato su `0` dall'associazione del modello e di conseguenza non viene rilevata alcuna entità nel database corrispondente a `id == 0`.</span><span class="sxs-lookup"><span data-stu-id="26008-658">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="26008-659">Il routing con attributi offre un controllo con granularità fine che consente di rendere l'ID obbligatorio per alcune azioni e non per altre.</span><span class="sxs-lookup"><span data-stu-id="26008-659">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="26008-660">Per convenzione la documentazione include i parametri facoltativi come `id` quando è probabile che appaiano nell'uso corretto.</span><span class="sxs-lookup"><span data-stu-id="26008-660">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="26008-661">Route multiple</span><span class="sxs-lookup"><span data-stu-id="26008-661">Multiple routes</span></span>

<span data-ttu-id="26008-662">È possibile aggiungere più route all'interno di `UseMvc` aggiungendo altre chiamate a `MapRoute`.</span><span class="sxs-lookup"><span data-stu-id="26008-662">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="26008-663">Questa operazione consente di definire più convenzioni o di aggiungere route convenzionali dedicate a un'azione specifica, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="26008-663">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="26008-664">La route `blog` è una *route convenzionale dedicata*, vale a dire che usa il sistema di routing convenzionale, ma è dedicata a un'azione specifica.</span><span class="sxs-lookup"><span data-stu-id="26008-664">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="26008-665">Poiché `controller` e `action` non appaiono nel modello di route come parametri, possono avere solo i valori predefiniti e quindi questa route eseguirà sempre il mapping all'azione `BlogController.Article`.</span><span class="sxs-lookup"><span data-stu-id="26008-665">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="26008-666">Le route sono ordinate nella raccolta di route e verranno elaborate nell'ordine in cui vengono aggiunte.</span><span class="sxs-lookup"><span data-stu-id="26008-666">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="26008-667">Quindi in questo esempio la route `blog` viene tentata prima della route `default`.</span><span class="sxs-lookup"><span data-stu-id="26008-667">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-668">Le *Route convenzionali dedicate* usano spesso parametri di route **catch-all** come `{*article}` per acquisire la parte rimanente del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-668">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="26008-669">In questo modo una route può diventare troppo "greedy", ovvero corrispondere agli URL che dovrebbero corrispondere ad altre route.</span><span class="sxs-lookup"><span data-stu-id="26008-669">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="26008-670">Posizionare le route "greedy" più avanti nella tabella delle route per risolvere il problema.</span><span class="sxs-lookup"><span data-stu-id="26008-670">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="26008-671">Fallback</span><span class="sxs-lookup"><span data-stu-id="26008-671">Fallback</span></span>

<span data-ttu-id="26008-672">Come parte dell'elaborazione della richiesta, MVC verifica che i valori di route possano essere usati per trovare un controller e un'azione nell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-672">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="26008-673">Se i valori di route non corrispondano a un'azione, la route non viene considerata una corrispondenza e viene tentata la route successiva.</span><span class="sxs-lookup"><span data-stu-id="26008-673">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="26008-674">Questa operazione si chiama *fallback* e viene eseguita allo scopo di semplificare i casi in cui le route convenzionali si sovrappongono.</span><span class="sxs-lookup"><span data-stu-id="26008-674">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="26008-675">Rimozione dell'ambiguità delle azioni</span><span class="sxs-lookup"><span data-stu-id="26008-675">Disambiguating actions</span></span>

<span data-ttu-id="26008-676">Quando due azioni corrispondono in base al routing, MVC deve rimuovere l'ambiguità per scegliere il candidato migliore, altrimenti genera un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="26008-676">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="26008-677">Ad esempio:</span><span class="sxs-lookup"><span data-stu-id="26008-677">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="26008-678">Questo controller definisce due azioni corrispondenti al percorso URL `/Products/Edit/17` e indirizzano i dati `{ controller = Products, action = Edit, id = 17 }`.</span><span class="sxs-lookup"><span data-stu-id="26008-678">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="26008-679">Si tratta di un modello tipico per i controller MVC in cui `Edit(int)` visualizza un modulo per modificare un prodotto e `Edit(int, Product)` elabora il modulo inviato.</span><span class="sxs-lookup"><span data-stu-id="26008-679">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="26008-680">Per poter eseguire l'operazione, MVC deve scegliere `Edit(int, Product)` se la richiesta è un `POST` HTTP e `Edit(int)` quando il verbo HTTP è un altro elemento.</span><span class="sxs-lookup"><span data-stu-id="26008-680">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="26008-681">`HttpPostAttribute` (`[HttpPost]`) è un'implementazione di `IActionConstraint` che consente di selezionare l'azione solo quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="26008-681">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="26008-682">La presenza di un oggetto `IActionConstraint` fa sì che `Edit(int, Product)` sia una corrispondenza migliore di `Edit(int)`, quindi `Edit(int, Product)` viene tentata per prima.</span><span class="sxs-lookup"><span data-stu-id="26008-682">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="26008-683">È necessario scrivere implementazioni personalizzate di `IActionConstraint` solo in scenari specializzati, ma è importante comprendere il ruolo degli attributi, ad esempio `HttpPostAttribute`. Attributi simili vengono definiti per altri verbi HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-683">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="26008-684">Nel routing convenzionale è normale che le azioni usino lo stesso nome di azione quando fanno parte di un flusso di lavoro `show form -> submit form`.</span><span class="sxs-lookup"><span data-stu-id="26008-684">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="26008-685">La comodità offerta da questo modello sarà più evidente dopo aver esaminato la sezione relativa a [IActionConstraint](#understanding-iactionconstraint).</span><span class="sxs-lookup"><span data-stu-id="26008-685">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="26008-686">Se vi sono più route corrispondenti e MVC non è in grado di trovare la route migliore, viene generata un'eccezione `AmbiguousActionException`.</span><span class="sxs-lookup"><span data-stu-id="26008-686">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="26008-687">Nomi di route</span><span class="sxs-lookup"><span data-stu-id="26008-687">Route names</span></span>

<span data-ttu-id="26008-688">Le stringhe `"blog"` e `"default"` degli esempi seguenti sono nomi di route:</span><span class="sxs-lookup"><span data-stu-id="26008-688">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="26008-689">I nomi di route consentono di assegnare a una route un nome logico, in modo che la route denominata possa essere usata per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-689">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="26008-690">Questo semplifica notevolmente la creazione degli URL quando l'ordinamento delle route può rendere complessa la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-690">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="26008-691">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-691">Route names must be unique application-wide.</span></span>

<span data-ttu-id="26008-692">I nomi di route non influiscono sulla corrispondenza degli URL o sulla gestione delle richieste, vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-692">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="26008-693">L'articolo sul [routing](xref:fundamentals/routing) contiene informazioni dettagliate sulla generazione di URL, inclusa la generazione negli helper specifici di MVC.</span><span class="sxs-lookup"><span data-stu-id="26008-693">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="26008-694">Routing con attributi</span><span class="sxs-lookup"><span data-stu-id="26008-694">Attribute routing</span></span>

<span data-ttu-id="26008-695">Il routing con attributi usa un set di attributi per eseguire il mapping delle azioni direttamente ai modelli di route.</span><span class="sxs-lookup"><span data-stu-id="26008-695">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="26008-696">Nell'esempio seguente viene usato l'oggetto `app.UseMvc();` nel metodo `Configure` e non viene passata alcuna route.</span><span class="sxs-lookup"><span data-stu-id="26008-696">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="26008-697">`HomeController` corrisponde a un set di URL simile a quello a cui corrisponderebbe la route predefinita `{controller=Home}/{action=Index}/{id?}`:</span><span class="sxs-lookup"><span data-stu-id="26008-697">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

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

<span data-ttu-id="26008-698">L'azione `HomeController.Index()` verrà eseguita per tutti i percorsi URL, `/`, `/Home` o `/Home/Index`.</span><span class="sxs-lookup"><span data-stu-id="26008-698">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-699">In questo esempio viene evidenziata un'importante differenza a livello di programmazione tra il routing con attributi e il routing convenzionale.</span><span class="sxs-lookup"><span data-stu-id="26008-699">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="26008-700">Il routing con attributi richiede più input per specificare una route, la route convenzionale predefinita gestisce le route in modo più conciso.</span><span class="sxs-lookup"><span data-stu-id="26008-700">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="26008-701">Tuttavia, il routing con attributi consente, e richiede, un controllo preciso dei modelli route da applicare a ogni azione.</span><span class="sxs-lookup"><span data-stu-id="26008-701">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="26008-702">Con il routing con attributi i nomi del controller e delle azioni **non** hanno alcun ruolo nella selezione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-702">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="26008-703">In questo esempio viene verificata la corrispondenza degli stessi URL dell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="26008-703">This example will match the same URLs as the previous example.</span></span>

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
> <span data-ttu-id="26008-704">I modelli di route precedenti non definiscono i parametri di route per `action`, `area` e `controller`.</span><span class="sxs-lookup"><span data-stu-id="26008-704">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="26008-705">In realtà, questi parametri di route non sono consentiti nelle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-705">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="26008-706">Poiché il modello di route è già associato a un'azione, non avrebbe senso analizzare il nome dell'azione dall'URL.</span><span class="sxs-lookup"><span data-stu-id="26008-706">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="26008-707">Routing con attributi Http[Verb]</span><span class="sxs-lookup"><span data-stu-id="26008-707">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="26008-708">Il routing con attributi può usare anche gli attributi `Http[Verb]`, ad esempio `HttpPostAttribute`.</span><span class="sxs-lookup"><span data-stu-id="26008-708">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="26008-709">Tutti questi attributi possono accettare un modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-709">All of these attributes can accept a route template.</span></span> <span data-ttu-id="26008-710">Questo esempio illustra due azioni che corrispondono allo stesso modello di route:</span><span class="sxs-lookup"><span data-stu-id="26008-710">This example shows two actions that match the same route template:</span></span>

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

<span data-ttu-id="26008-711">Per un percorso URL come `/products` l'azione `ProductsApi.ListProducts` verrà eseguita quando il verbo HTTP è `GET` e `ProductsApi.CreateProduct` verrà eseguita quando il verbo HTTP è `POST`.</span><span class="sxs-lookup"><span data-stu-id="26008-711">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="26008-712">Nel routing con attributi l'URL viene prima confrontato con il set di modelli di route definiti dagli attributi della route.</span><span class="sxs-lookup"><span data-stu-id="26008-712">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="26008-713">Quando un modello di route corrisponde, vengono applicati i vincoli `IActionConstraint` per determinare quali azioni possono essere eseguite.</span><span class="sxs-lookup"><span data-stu-id="26008-713">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="26008-714">Quando si compila un'API REST, è raro che si voglia usare `[Route(...)]` su un metodo di azione perché l'azione accetta tutti i metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-714">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="26008-715">Si consiglia di usare l'oggetto `Http*Verb*Attributes`, più specifico, per essere precisi circa gli elementi supportati dall'API.</span><span class="sxs-lookup"><span data-stu-id="26008-715">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="26008-716">I client delle API REST devono sapere quali percorsi e verbi HTTP devono essere mappati a operazioni logiche specifiche.</span><span class="sxs-lookup"><span data-stu-id="26008-716">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="26008-717">Poiché una route con attributi si applica a un'azione specifica, è facile fare in modo che i parametri siano richiesti come parte della definizione del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-717">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="26008-718">In questo esempio `id` è richiesto come parte del percorso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-718">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="26008-719">L'azione `ProductsApi.GetProduct(int)` verrà eseguita per un percorso URL come `/products/3`, ma non per un percorso URL come `/products`.</span><span class="sxs-lookup"><span data-stu-id="26008-719">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="26008-720">Vedere [Routing](xref:fundamentals/routing) per una descrizione completa dei modelli di route e delle opzioni correlate.</span><span class="sxs-lookup"><span data-stu-id="26008-720">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="26008-721">Nome di route</span><span class="sxs-lookup"><span data-stu-id="26008-721">Route Name</span></span>

<span data-ttu-id="26008-722">Nell’esempio di codice riportato di seguito viene definito un *nome di route* di `Products_List`:</span><span class="sxs-lookup"><span data-stu-id="26008-722">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="26008-723">I nomi di route possono essere usati per generare un URL in base a un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="26008-723">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="26008-724">I nomi di route non influiscono sul comportamento del routing riguardo alla corrispondenza degli URL e vengono usati solo per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-724">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="26008-725">I nomi delle route devono essere univoci a livello di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-725">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-726">Confrontare questa opzione con la *route predefinita* convenzionale, che definisce il parametro `id` come facoltativo (`{id?}`).</span><span class="sxs-lookup"><span data-stu-id="26008-726">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="26008-727">La possibilità di specificare in modo preciso le API presenta dei vantaggi, ad esempio consente di inviare `/products` e `/products/5` ad azioni  differenti.</span><span class="sxs-lookup"><span data-stu-id="26008-727">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="26008-728">Combinazione di route</span><span class="sxs-lookup"><span data-stu-id="26008-728">Combining routes</span></span>

<span data-ttu-id="26008-729">Per rendere il routing con attributi meno ripetitivo, gli attributi di route del controller vengono combinati con gli attributi di route delle singole azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-729">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="26008-730">I modelli di route definiti per il controller vengono anteposti ai modelli di route delle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-730">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="26008-731">Inserendo un attributo di route nel controller, **tutte** le azioni presenti nel controller useranno il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-731">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

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

<span data-ttu-id="26008-732">In questo esempio il percorso URL `/products` può corrispondere a `ProductsApi.ListProducts` e il percorso URL `/products/5` può corrispondere a `ProductsApi.GetProduct(int)`.</span><span class="sxs-lookup"><span data-stu-id="26008-732">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="26008-733">Entrambe le azioni corrispondono solo a HTTP `GET` perché sono contrassegnate con `HttpGetAttribute` .</span><span class="sxs-lookup"><span data-stu-id="26008-733">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="26008-734">I modelli di route applicati a un'azione che iniziano con `/` o `~/` non vengono combinati con i modelli di route applicati al controller.</span><span class="sxs-lookup"><span data-stu-id="26008-734">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="26008-735">Questo esempio illustra la corrispondenza di un set di percorsi URL simile alla *route predefinita*.</span><span class="sxs-lookup"><span data-stu-id="26008-735">This example matches a set of URL paths similar to the *default route*.</span></span>

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

### <a name="ordering-attribute-routes"></a><span data-ttu-id="26008-736">Ordinamento delle route con attributi</span><span class="sxs-lookup"><span data-stu-id="26008-736">Ordering attribute routes</span></span>

<span data-ttu-id="26008-737">Diversamente dalle route convenzionali, che vengono eseguite in un ordine definito, il routing degli attributi compila un albero e trova la corrispondenza di tutte le route simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="26008-737">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="26008-738">Si comporta come se le voci delle route seguissero un ordine ideale in cui le route più specifiche vengono probabilmente eseguite prima delle route più generali.</span><span class="sxs-lookup"><span data-stu-id="26008-738">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="26008-739">Ad esempio, una route come `blog/search/{topic}` è più specifica di una route come `blog/{*article}`.</span><span class="sxs-lookup"><span data-stu-id="26008-739">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="26008-740">In termini logici la route `blog/search/{topic}` viene eseguita per prima, per impostazione predefinita, perché questo è l'ordine più plausibile.</span><span class="sxs-lookup"><span data-stu-id="26008-740">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="26008-741">Usando il routing convenzionale, lo sviluppatore ordina le route in base alle proprie esigenze.</span><span class="sxs-lookup"><span data-stu-id="26008-741">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="26008-742">Nelle route con attributi si può configurare un ordine usando la proprietà `Order` di tutti gli attributi di route specificati dal framework.</span><span class="sxs-lookup"><span data-stu-id="26008-742">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="26008-743">Le route vengono elaborate in base a un ordinamento crescente della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="26008-743">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="26008-744">L'ordine predefinito è `0`.</span><span class="sxs-lookup"><span data-stu-id="26008-744">The default order is `0`.</span></span> <span data-ttu-id="26008-745">L'impostazione di una route usando `Order = -1` viene eseguita prima delle route per cui non è impostato un ordine.</span><span class="sxs-lookup"><span data-stu-id="26008-745">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="26008-746">L'impostazione di una route usando `Order = 1` viene eseguito dopo l'ordinamento predefinito delle route.</span><span class="sxs-lookup"><span data-stu-id="26008-746">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="26008-747">Evitare la dipendenza da `Order`.</span><span class="sxs-lookup"><span data-stu-id="26008-747">Avoid depending on `Order`.</span></span> <span data-ttu-id="26008-748">Se lo spazio URL richiede i valori in un ordine esplicito per indirizzare correttamente i dati, si può probabilmente creare confusione anche per i client.</span><span class="sxs-lookup"><span data-stu-id="26008-748">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="26008-749">In genere il routing con attributi seleziona la route corretta con la corrispondenza di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-749">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="26008-750">Se l'ordine predefinito usato per la generazione di URL non funziona, usare il nome della route come override è in genere più semplice che applicare la proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="26008-750">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="26008-751">RazorIl routing di pagine e il routing del controller MVC condividono un'implementazione.</span><span class="sxs-lookup"><span data-stu-id="26008-751">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="26008-752">Le informazioni sull'ordine di route negli Razor argomenti pagine sono disponibili nelle [ Razor pagine Route e app Conventions: Order Route](xref:razor-pages/razor-pages-conventions#route-order).</span><span class="sxs-lookup"><span data-stu-id="26008-752">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="26008-753">Sostituzione dei token nei modelli di route ([controller], [action], [area])</span><span class="sxs-lookup"><span data-stu-id="26008-753">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="26008-754">Per praticità, le route degli attributi supportano la *sostituzione dei token* mediante l'inclusione di un token tra parentesi quadre ( `[` , `]` ).</span><span class="sxs-lookup"><span data-stu-id="26008-754">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="26008-755">I token `[action]`, `[area]` e `[controller]` vengono sostituiti con i valori di nome dell'azione, nome dell'area e nome del controller dall'azione in cui è definita la route.</span><span class="sxs-lookup"><span data-stu-id="26008-755">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="26008-756">Nell'esempio seguente le azioni corrispondono ai percorsi URL come descritto nei commenti:</span><span class="sxs-lookup"><span data-stu-id="26008-756">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="26008-757">La sostituzione dei token avviene come ultimo passaggio della creazione delle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-757">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="26008-758">L'esempio precedente si comporterà come il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-758">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="26008-759">Le route con attributi possono anche essere combinate con l'ereditarietà.</span><span class="sxs-lookup"><span data-stu-id="26008-759">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="26008-760">Ciò risulta particolarmente efficace se combinato con la sostituzione dei token.</span><span class="sxs-lookup"><span data-stu-id="26008-760">This is particularly powerful combined with token replacement.</span></span>

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

<span data-ttu-id="26008-761">La sostituzione dei token si applica anche ai nomi di route definiti dalle route con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-761">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="26008-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` genera un nome di route univoco per ogni azione.</span><span class="sxs-lookup"><span data-stu-id="26008-762">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="26008-763">Per verificare la corrispondenza del delimitatore letterale della sostituzione di token `[` o `]`, eseguirne l'escape ripetendo il carattere (`[[` o `]]`).</span><span class="sxs-lookup"><span data-stu-id="26008-763">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="26008-764">Usare un trasformatore di parametri per personalizzare la sostituzione dei token</span><span class="sxs-lookup"><span data-stu-id="26008-764">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="26008-765">La sostituzione dei token può essere personalizzata usando un trasformatore di parametri.</span><span class="sxs-lookup"><span data-stu-id="26008-765">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="26008-766">Un trasformatore di parametri implementa `IOutboundParameterTransformer` e trasforma il valore dei parametri.</span><span class="sxs-lookup"><span data-stu-id="26008-766">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="26008-767">Ad esempio, un trasformatore di parametri `SlugifyParameterTransformer` personalizzato cambia il valore di route `SubscriptionManagement` in `subscription-management`.</span><span class="sxs-lookup"><span data-stu-id="26008-767">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="26008-768">`RouteTokenTransformerConvention` è una convenzione del modello di applicazione che:</span><span class="sxs-lookup"><span data-stu-id="26008-768">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="26008-769">Applica un trasformatore di parametri a tutte le route di attributi in un'applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-769">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="26008-770">Personalizza i valori dei token delle route di attributi quando vengono sostituiti.</span><span class="sxs-lookup"><span data-stu-id="26008-770">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="26008-771">La convenzione `RouteTokenTransformerConvention` è registrata come un'opzione in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="26008-771">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

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

### <a name="multiple-routes"></a><span data-ttu-id="26008-772">Route multiple</span><span class="sxs-lookup"><span data-stu-id="26008-772">Multiple Routes</span></span>

<span data-ttu-id="26008-773">Il routing con attributi supporta la definizione di più route che raggiungono la stessa azione.</span><span class="sxs-lookup"><span data-stu-id="26008-773">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="26008-774">L'uso più comune è simulare il comportamento della *route convenzionale predefinita* come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-774">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="26008-775">Inserire più attributi di route nel controller significa che verranno tutti combinati con tutti gli attributi di route nei metodi dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-775">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

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

<span data-ttu-id="26008-776">Quando più attributi di route (che implementano `IActionConstraint`) sono inseriti in un'azione, ogni vincolo dell'azione viene combinato con il modello di route dall'attributo che lo definisce.</span><span class="sxs-lookup"><span data-stu-id="26008-776">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

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
> <span data-ttu-id="26008-777">Benché l'uso di più route per le azioni possa sembrare efficace, è preferibile mantenere lo spazio URL dell'applicazione semplice e ben definito.</span><span class="sxs-lookup"><span data-stu-id="26008-777">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="26008-778">Usare più route per le azioni solo se necessario, ad esempio per supportare i client esistenti.</span><span class="sxs-lookup"><span data-stu-id="26008-778">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="26008-779">Definizione di parametri facoltativi, valori predefiniti e vincoli della route con attributi</span><span class="sxs-lookup"><span data-stu-id="26008-779">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="26008-780">Le route con attributi supportano la stessa sintassi inline delle route convenzionali per specificare i parametri facoltativi, i valori predefiniti e i vincoli.</span><span class="sxs-lookup"><span data-stu-id="26008-780">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="26008-781">Vedere [Riferimento per i modelli di route](xref:fundamentals/routing#route-template-reference) per una descrizione dettagliata della sintassi del modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-781">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="26008-782">Attributi di route personalizzati che usano `IRouteTemplateProvider`</span><span class="sxs-lookup"><span data-stu-id="26008-782">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="26008-783">Tutti gli attributi di route disponibili nel framework (`[Route(...)]`, `[HttpGet(...)]` e così via) implementano l'interfaccia `IRouteTemplateProvider`.</span><span class="sxs-lookup"><span data-stu-id="26008-783">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="26008-784">MVC cerca gli attributi per le classi del controller e i metodi delle azioni all'avvio dell'app e usa quelli che implementano `IRouteTemplateProvider` per compilare il set iniziale di route.</span><span class="sxs-lookup"><span data-stu-id="26008-784">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="26008-785">È possibile implementare `IRouteTemplateProvider` per definire i propri attributi di route.</span><span class="sxs-lookup"><span data-stu-id="26008-785">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="26008-786">Ogni `IRouteTemplateProvider` consente di definire una singola route con un modello di route, un ordinamento e un nome personalizzati:</span><span class="sxs-lookup"><span data-stu-id="26008-786">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="26008-787">L'attributo dell'esempio precedente imposta automaticamente `Template` su `"api/[controller]"` quando si applica `[MyApiController]`.</span><span class="sxs-lookup"><span data-stu-id="26008-787">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="26008-788">Uso del modello applicativo per personalizzare le route con attributi</span><span class="sxs-lookup"><span data-stu-id="26008-788">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="26008-789">Il *modello applicativo* è un modello a oggetti creato all'avvio con tutti i metadati usati da MVC per indirizzare ed eseguire le azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-789">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="26008-790">Il *modello applicativo* include tutti i dati raccolti da attributi di route (attraverso `IRouteTemplateProvider`).</span><span class="sxs-lookup"><span data-stu-id="26008-790">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="26008-791">È possibile scrivere *convenzioni* per modificare il modello applicativo in fase di avvio per personalizzare il comportamento del routing.</span><span class="sxs-lookup"><span data-stu-id="26008-791">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="26008-792">Questa sezione illustra un semplice esempio di personalizzazione del routing con il modello applicativo.</span><span class="sxs-lookup"><span data-stu-id="26008-792">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="26008-793">Routing misto: routing con attributi e routing convenzionale</span><span class="sxs-lookup"><span data-stu-id="26008-793">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="26008-794">Le applicazioni MVC sono in grado di usare una combinazione di routing convenzionale e routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-794">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="26008-795">In genere le route convenzionali vengono usate per i controller che gestiscono le pagine HTML per i browser e le route con attributi per i controller che gestiscono le API REST.</span><span class="sxs-lookup"><span data-stu-id="26008-795">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="26008-796">Le azioni vengono indirizzate in modo convenzionale o con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-796">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="26008-797">Se una route viene inserita nel controller o nell'azione, viene indirizzata con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-797">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="26008-798">Le azioni che definiscono le route con attributi non possono essere raggiunte usando le route convenzionali e viceversa.</span><span class="sxs-lookup"><span data-stu-id="26008-798">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="26008-799">**Qualsiasi** attributo di route del controller rende tutte le azioni presenti nel controller indirizzate con attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-799">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-800">Ciò che distingue i due tipi di sistema di routing è il processo applicato dopo che si è verificata la corrispondenza di un URL con un modello di route.</span><span class="sxs-lookup"><span data-stu-id="26008-800">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="26008-801">Nel routing convenzionale i valori di route della corrispondenza vengono usati per scegliere l'azione e il controller da una tabella di ricerca di tutte le azioni indirizzate in modo convenzionale.</span><span class="sxs-lookup"><span data-stu-id="26008-801">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="26008-802">Nel routing con attributi ogni modello è già associato a un'azione e non è necessaria alcuna ulteriore ricerca.</span><span class="sxs-lookup"><span data-stu-id="26008-802">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="26008-803">Segmenti complessi</span><span class="sxs-lookup"><span data-stu-id="26008-803">Complex segments</span></span>

<span data-ttu-id="26008-804">I segmenti complessi (ad esempio, `[Route("/dog{token}cat")]`), vengono elaborati individuando corrispondenze per i valori letterali da destra a sinistra in modalità non-greedy.</span><span class="sxs-lookup"><span data-stu-id="26008-804">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="26008-805">Vedere [il codice sorgente](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) per una descrizione.</span><span class="sxs-lookup"><span data-stu-id="26008-805">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="26008-806">Per altre informazioni, vedere [questo problema](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span><span class="sxs-lookup"><span data-stu-id="26008-806">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="26008-807">Generazione di URL</span><span class="sxs-lookup"><span data-stu-id="26008-807">URL Generation</span></span>

<span data-ttu-id="26008-808">Le applicazioni MVC sono in grado di usare le funzionalità di generazione di URL del routing per generare i collegamenti URL alle azioni.</span><span class="sxs-lookup"><span data-stu-id="26008-808">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="26008-809">La generazione di URL evita la codifica degli URL e consente di rendere il codice più affidabile e gestibile.</span><span class="sxs-lookup"><span data-stu-id="26008-809">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="26008-810">Questa sezione illustra le funzionalità di generazione di URL disponibili in MVC e tratta solo le nozioni di base sul funzionamento della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-810">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="26008-811">Vedere [Routing](xref:fundamentals/routing) per una descrizione dettagliata della generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-811">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="26008-812">L'interfaccia `IUrlHelper` è l'elemento sottostante dell'infrastruttura tra MVC e il routing per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-812">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="26008-813">È possibile trovare un'istanza di `IUrlHelper` disponibile usando la proprietà `Url` in controller, visualizzazioni e componenti di visualizzazione.</span><span class="sxs-lookup"><span data-stu-id="26008-813">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="26008-814">In questo esempio l'interfaccia `IUrlHelper` viene usata attraverso la proprietà `Controller.Url` per generare un URL a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="26008-814">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="26008-815">Se l'applicazione usa la route convenzionale predefinita, il valore della variabile `url` sarà la stringa del percorso URL `/UrlGeneration/Destination`.</span><span class="sxs-lookup"><span data-stu-id="26008-815">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="26008-816">Il percorso URL viene creato dal routing combinando i valori di route della richiesta corrente (valori di ambiente) con i valori passati a `Url.Action` e sostituendo tali valori nel modello di route:</span><span class="sxs-lookup"><span data-stu-id="26008-816">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="26008-817">Il valore di ogni parametro di route del modello di route viene sostituito attraverso la corrispondenza dei nomi con i valori e i valori di ambiente.</span><span class="sxs-lookup"><span data-stu-id="26008-817">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="26008-818">Per un parametro di route senza un valore si può usare un valore predefinito, se ne esiste uno, oppure ignorare il parametro se è facoltativo (come nel caso di `id` in questo esempio).</span><span class="sxs-lookup"><span data-stu-id="26008-818">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="26008-819">La generazione di URL avrà esito negativo se un parametro di route obbligatorio non ha un valore corrispondente.</span><span class="sxs-lookup"><span data-stu-id="26008-819">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="26008-820">Se la generazione di URL non riesce per una route, viene tentata la route successiva finché non vengono tentate tutte le route o viene trovata una corrispondenza.</span><span class="sxs-lookup"><span data-stu-id="26008-820">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="26008-821">Nell'esempio di `Url.Action` precedente si presuppone che il routing sia convenzionale, ma la generazione di URL funziona in modo analogo con il routing con attributi, sebbene i concetti siano differenti.</span><span class="sxs-lookup"><span data-stu-id="26008-821">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="26008-822">Con il routing convenzionale i valori di route vengono usati per espandere un modello e i valori di route per `controller` e `action` in genere appaiono in quel modello. Questo procedimento funziona perché gli URL corrispondenti in base al routing rispettano una *convenzione*.</span><span class="sxs-lookup"><span data-stu-id="26008-822">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="26008-823">Nel routing con attributi i valori di route per `controller` e `action` non possono essere visualizzati nel modello ma vengono usati per individuare il modello da usare.</span><span class="sxs-lookup"><span data-stu-id="26008-823">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="26008-824">Questo esempio usa il routing con attributi:</span><span class="sxs-lookup"><span data-stu-id="26008-824">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="26008-825">MVC compila una tabella di ricerca di tutte le azioni indirizzate con attributi e stabilisce la corrispondenza dei valori `controller` e `action` per selezionare il modello di route da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-825">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="26008-826">Nell'esempio precedente viene generato `custom/url/to/destination`.</span><span class="sxs-lookup"><span data-stu-id="26008-826">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="26008-827">Generazione di URL in base al nome dell'azione</span><span class="sxs-lookup"><span data-stu-id="26008-827">Generating URLs by action name</span></span>

<span data-ttu-id="26008-828">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="26008-828">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="26008-829">`Action`) e tutti gli overload correlati sono basati sull'idea che si voglia definire l'oggetto a cui ci si sta collegando specificando un nome di controller e un nome di azione.</span><span class="sxs-lookup"><span data-stu-id="26008-829">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-830">Quando si usa `Url.Action`, i valori di route correnti per `controller` e `action` vengono specificati automaticamente: i valori di `controller` e `action` fanno parte sia dei *valori di ambiente* \*\*sia dei \*\* *valori*.</span><span class="sxs-lookup"><span data-stu-id="26008-830">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="26008-831">Il metodo `Url.Action`, usa sempre i valori correnti di `action` e `controller` e genererà un percorso URL che indirizza all'azione corrente.</span><span class="sxs-lookup"><span data-stu-id="26008-831">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="26008-832">Il routing tenta di usare i valori nei valori di ambiente per ottenere le informazioni non specificate durante la generazione di un URL.</span><span class="sxs-lookup"><span data-stu-id="26008-832">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="26008-833">Usando una route come `{a}/{b}/{c}/{d}` e i valori di ambiente `{ a = Alice, b = Bob, c = Carol, d = David }`, il routing ha informazioni sufficienti per generare un URL senza valori aggiuntivi, poiché tutti i parametri di route hanno un valore.</span><span class="sxs-lookup"><span data-stu-id="26008-833">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="26008-834">Se è stato aggiunto il valore `{ d = Donovan }`, il valore `{ d = David }` viene ignorato e il percorso URL generato è `Alice/Bob/Carol/Donovan`.</span><span class="sxs-lookup"><span data-stu-id="26008-834">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="26008-835">I percorsi URL sono gerarchici.</span><span class="sxs-lookup"><span data-stu-id="26008-835">URL paths are hierarchical.</span></span> <span data-ttu-id="26008-836">Nell'esempio precedente, se è stato aggiunto il valore `{ c = Cheryl }`, entrambi i valori `{ c = Carol, d = David }` vengono ignorati.</span><span class="sxs-lookup"><span data-stu-id="26008-836">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="26008-837">In questo caso non è più disponibile un valore per `d` e la generazione dell'URL avrà esito negativo.</span><span class="sxs-lookup"><span data-stu-id="26008-837">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="26008-838">Sarà necessario specificare un valore per `c` e `d`.</span><span class="sxs-lookup"><span data-stu-id="26008-838">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="26008-839">In apparenza questo problema si può risolvere con la route predefinita (`{controller}/{action}/{id?}`), ma in realtà questo comportamento si verifica raramente perché `Url.Action` specifica sempre in modo esplicito un valore `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="26008-839">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="26008-840">Anche gli overload di `Url.Action` di maggiore durata accettano anche un oggetto *valori di route* aggiuntivo per specificare i valori per i parametri di route diversi da `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="26008-840">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="26008-841">In genere viene usato con `id` come `Url.Action("Buy", "Products", new { id = 17 })`.</span><span class="sxs-lookup"><span data-stu-id="26008-841">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="26008-842">Per convenzione l'oggetto *valori di route* è in genere un oggetto di tipo anonimo, ma può anche essere un `IDictionary<>` o un *normale oggetto .NET*.</span><span class="sxs-lookup"><span data-stu-id="26008-842">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="26008-843">I valori di route aggiuntivi che non corrispondono a parametri di route vengono inseriti nella stringa di query.</span><span class="sxs-lookup"><span data-stu-id="26008-843">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="26008-844">Per creare un URL assoluto, usare un overload che accetta un `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="26008-844">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="26008-845">Generazione di URL in base alla route</span><span class="sxs-lookup"><span data-stu-id="26008-845">Generating URLs by route</span></span>

<span data-ttu-id="26008-846">Il codice precedente illustra come generare un URL passando il nome del controller e dell'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-846">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="26008-847">`IUrlHelper` specifica anche la famiglia di metodi `Url.RouteUrl`.</span><span class="sxs-lookup"><span data-stu-id="26008-847">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="26008-848">Questi metodi sono simili a `Url.Action`, ma non copiano i valori correnti di `action` e `controller` nei valori di route.</span><span class="sxs-lookup"><span data-stu-id="26008-848">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="26008-849">L'uso più comune consiste nello specificare un nome di route per usare un percorso specifico per generare l'URL, in genere *senza* specificare un nome di controller o azione.</span><span class="sxs-lookup"><span data-stu-id="26008-849">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="26008-850">Generazione di URL in HTML</span><span class="sxs-lookup"><span data-stu-id="26008-850">Generating URLs in HTML</span></span>

<span data-ttu-id="26008-851">`IHtmlHelper` specifica i metodi `HtmlHelper``Html.BeginForm` e `Html.ActionLink` per generare rispettivamente gli elementi `<form>` e `<a>`.</span><span class="sxs-lookup"><span data-stu-id="26008-851">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="26008-852">Questi metodi usano il metodo `Url.Action` per generare un URL e accettano argomenti simili.</span><span class="sxs-lookup"><span data-stu-id="26008-852">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="26008-853">Gli oggetti `Url.RouteUrl` complementi di `HtmlHelper` sono `Html.BeginRouteForm` e `Html.RouteLink` e hanno una funzionalità simile.</span><span class="sxs-lookup"><span data-stu-id="26008-853">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="26008-854">Gli helper tag generano gli URL attraverso l'helper tag `form` e l'helper tag `<a>`.</span><span class="sxs-lookup"><span data-stu-id="26008-854">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="26008-855">Entrambi usano `IUrlHelper` per la propria implementazione.</span><span class="sxs-lookup"><span data-stu-id="26008-855">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="26008-856">Per altre informazioni, vedere [Uso dei moduli](../views/working-with-forms.md).</span><span class="sxs-lookup"><span data-stu-id="26008-856">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="26008-857">All'interno delle visualizzazioni `IUrlHelper` è reso disponibile dalla proprietà `Url` per tutte le generazioni di URL ad hoc che non rientrano nelle situazioni descritte in precedenza.</span><span class="sxs-lookup"><span data-stu-id="26008-857">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="26008-858">Generazione di URL nei risultati delle azioni</span><span class="sxs-lookup"><span data-stu-id="26008-858">Generating URLS in Action Results</span></span>

<span data-ttu-id="26008-859">Negli esempi precedenti è stato usato `IUrlHelper` in un controller, ma l'uso più comune in un controller è generare un URL come parte del risultato di un'azione.</span><span class="sxs-lookup"><span data-stu-id="26008-859">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="26008-860">Le classi di base `ControllerBase` e `Controller` offrono metodi pratici per i risultati delle azioni che fanno riferimento a un'altra azione.</span><span class="sxs-lookup"><span data-stu-id="26008-860">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="26008-861">Un uso tipico è eseguire il reindirizzamento dopo aver accettato l'input dell'utente.</span><span class="sxs-lookup"><span data-stu-id="26008-861">One typical usage is to redirect after accepting user input.</span></span>

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

<span data-ttu-id="26008-862">I metodi factory dei risultati delle azioni seguono un modello simile ai metodi per `IUrlHelper`.</span><span class="sxs-lookup"><span data-stu-id="26008-862">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="26008-863">Caso speciale per le route convenzionali dedicate</span><span class="sxs-lookup"><span data-stu-id="26008-863">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="26008-864">Nel routing convenzionale è possibile usare un tipo speciale di definizione di route denominata *route convenzionale dedicata*.</span><span class="sxs-lookup"><span data-stu-id="26008-864">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="26008-865">Nell'esempio seguente la route denominata `blog` è una route convenzionale dedicata.</span><span class="sxs-lookup"><span data-stu-id="26008-865">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="26008-866">Usando queste definizioni di route, `Url.Action("Index", "Home")` genera il percorso URL `/` con la route `default` per un motivo ben preciso.</span><span class="sxs-lookup"><span data-stu-id="26008-866">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="26008-867">Si potrebbe pensare che i valori di route `{ controller = Home, action = Index }` siano sufficienti per generare un URL usando `blog` e che il risultato sia `/blog?action=Index&controller=Home`.</span><span class="sxs-lookup"><span data-stu-id="26008-867">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="26008-868">Le route convenzionali dedicate si basano su un comportamento speciale dei valori predefiniti per i quali non esiste un parametro di route corrispondente che impedisce alla route di essere troppo "greedy" con la generazione degli URL.</span><span class="sxs-lookup"><span data-stu-id="26008-868">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="26008-869">In questo caso i valori predefiniti sono `{ controller = Blog, action = Article }` e né `controller` né `action` vengono visualizzati come parametri di route.</span><span class="sxs-lookup"><span data-stu-id="26008-869">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="26008-870">Quando il routing esegue la generazione di URL, i valori specificati devono corrispondere ai valori predefiniti.</span><span class="sxs-lookup"><span data-stu-id="26008-870">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="26008-871">La generazione di URL che usa `blog` avrà esito negativo perché i valori `{ controller = Home, action = Index }` non corrispondono a `{ controller = Blog, action = Article }`.</span><span class="sxs-lookup"><span data-stu-id="26008-871">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="26008-872">Il routing quindi esegue il fallback per provare `default`, che ha esito positivo.</span><span class="sxs-lookup"><span data-stu-id="26008-872">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="26008-873">Aree</span><span class="sxs-lookup"><span data-stu-id="26008-873">Areas</span></span>

<span data-ttu-id="26008-874">Le [aree](areas.md) sono una funzionalità di MVC che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi di routing separato (per le azioni del controller) e struttura di cartelle (per le visualizzazioni).</span><span class="sxs-lookup"><span data-stu-id="26008-874">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="26008-875">L'uso delle aree consente a un'applicazione di usare più controller con lo stesso nome, purché abbiano *aree* diverse.</span><span class="sxs-lookup"><span data-stu-id="26008-875">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="26008-876">Usando le aree si crea una gerarchia per il routing aggiungendo un altro parametro di route, `area` a `controller` e `action`.</span><span class="sxs-lookup"><span data-stu-id="26008-876">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="26008-877">Questa sezione illustra il modo in cui il routing interagisce con le aree. Vedere [Aree](areas.md) per informazioni dettagliate sull'uso delle aree con le visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="26008-877">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="26008-878">Nell'esempio seguente MVC viene configurato in modo da usare la route convenzionale predefinita e una *route di area* per un'area denominata `Blog`:</span><span class="sxs-lookup"><span data-stu-id="26008-878">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="26008-879">Nella corrispondenza di un percorso URL come `/Manage/Users/AddUser` la prima route produrrà i valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="26008-879">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="26008-880">Il valore di route `area` è generato da un valore predefinito per `area`, infatti la route creata da `MapAreaRoute` equivale alla seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-880">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="26008-881">`MapAreaRoute` crea una route che usa sia un valore predefinito che un vincolo per `area` usando il nome di area specificato, in questo caso `Blog`.</span><span class="sxs-lookup"><span data-stu-id="26008-881">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="26008-882">Il valore predefinito assicura che la route generi sempre `{ area = Blog, ... }`, il vincolo richiede il valore `{ area = Blog, ... }` per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-882">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="26008-883">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="26008-883">Conventional routing is order-dependent.</span></span> <span data-ttu-id="26008-884">In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="26008-884">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="26008-885">Se si usa l'esempio precedente, i valori di route corrispondono all'azione seguente:</span><span class="sxs-lookup"><span data-stu-id="26008-885">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="26008-886">`AreaAttribute` è ciò che denota un controller come parte di un'area, questo controller in particolare si trova nell'area `Blog`.</span><span class="sxs-lookup"><span data-stu-id="26008-886">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="26008-887">I controller senza un attributo `[Area]` non sono membri di alcuna area e **non** corrispondono quando il valore di route `area` viene specificato dal routing.</span><span class="sxs-lookup"><span data-stu-id="26008-887">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="26008-888">Nell'esempio seguente solo il primo controller indicato può corrispondere ai valori di route `{ area = Blog, controller = Users, action = AddUser }`.</span><span class="sxs-lookup"><span data-stu-id="26008-888">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="26008-889">Lo spazio dei nomi di ogni controller viene indicato qui per motivi di completezza, per evitare conflitti di denominazione nei controller e la generazione di errori del compilatore.</span><span class="sxs-lookup"><span data-stu-id="26008-889">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="26008-890">Gli spazi dei nomi delle classi non hanno effetto sul routing di MVC.</span><span class="sxs-lookup"><span data-stu-id="26008-890">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="26008-891">I primi due controller sono membri di aree e corrispondono solo quando viene specificato il relativo nome di area dal valore di route `area`.</span><span class="sxs-lookup"><span data-stu-id="26008-891">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="26008-892">Il terzo controller non è un membro di un'area e può corrispondere solo quando non vengono specificati valori per `area` dal routing.</span><span class="sxs-lookup"><span data-stu-id="26008-892">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="26008-893">In termini di corrispondenza con *nessun valore*, l'assenza del valore `area` è come se il valore per `area` fosse Null o la stringa vuota.</span><span class="sxs-lookup"><span data-stu-id="26008-893">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="26008-894">Quando si esegue un'azione all'interno di un'area, il valore di route per `area` sarà disponibile come *valore di ambiente* per il routing da usare per la generazione di URL.</span><span class="sxs-lookup"><span data-stu-id="26008-894">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="26008-895">Ciò significa che per impostazione predefinita le aree funzionano *temporaneamente* per la generazione di URL, come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="26008-895">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="26008-896">Informazioni su IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="26008-896">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="26008-897">Questa sezione offre un'analisi approfondita degli elementi interni del framework e del modo in cui MVC sceglie un'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="26008-897">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="26008-898">Un'applicazione tipica non richiede un oggetto `IActionConstraint` personalizzato</span><span class="sxs-lookup"><span data-stu-id="26008-898">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="26008-899">Probabilmente l'utente ha già usato `IActionConstraint` anche se non ha familiarità con l'interfaccia.</span><span class="sxs-lookup"><span data-stu-id="26008-899">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="26008-900">L'attributo `[HttpGet]` e gli attributi `[Http-VERB]` dello stesso tipo implementano `IActionConstraint` per limitare l'esecuzione di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="26008-900">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="26008-901">Se si usa la route convenzionale predefinita, il percorso URL `/Products/Edit` produce i valori `{ controller = Products, action = Edit }`, che corrispondono a **entrambe** le azioni illustrate qui.</span><span class="sxs-lookup"><span data-stu-id="26008-901">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="26008-902">Nella terminologia di `IActionConstraint` si dice che entrambe le azioni sono considerate candidati poiché corrispondono entrambe ai dati della route.</span><span class="sxs-lookup"><span data-stu-id="26008-902">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="26008-903">Quando viene eseguito `HttpGetAttribute` indica che *Edit()* è una corrispondenza per *GET* e non è una corrispondenza per qualsiasi altro verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-903">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="26008-904">L'azione `Edit(...)` non ha tutti i vincoli definiti e quindi corrisponde a qualsiasi verbo HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-904">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="26008-905">Presupponendo l'uso di un oggetto `POST`, corrisponde solo `Edit(...)`.</span><span class="sxs-lookup"><span data-stu-id="26008-905">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="26008-906">Per un oggetto `GET` possono invece corrispondere entrambe le azioni, tuttavia un'azione con `IActionConstraint` viene sempre considerata *migliore* rispetto a un'azione senza tale oggetto.</span><span class="sxs-lookup"><span data-stu-id="26008-906">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="26008-907">Quindi, dal momento che `Edit()` ha `[HttpGet]` è considerata più specifica e verrà selezionata se entrambe le azioni possono corrispondere.</span><span class="sxs-lookup"><span data-stu-id="26008-907">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="26008-908">Concettualmente, `IActionConstraint` è una forma di *overload*, ma anziché sostituire i metodi con lo stesso nome, supporta l'overload tra le azioni che corrispondono allo stesso URL.</span><span class="sxs-lookup"><span data-stu-id="26008-908">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="26008-909">Anche il routing con attributi usa `IActionConstraint` e può accadere che azioni di controller diversi vengano entrambe considerate candidati.</span><span class="sxs-lookup"><span data-stu-id="26008-909">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="26008-910">Implementazione di IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="26008-910">Implementing IActionConstraint</span></span>

<span data-ttu-id="26008-911">Il modo più semplice di implementare un oggetto `IActionConstraint` è creare una classe derivata da `System.Attribute` e inserirla nelle azioni e nel controller.</span><span class="sxs-lookup"><span data-stu-id="26008-911">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="26008-912">MVC rileverà automaticamente tutti gli oggetti `IActionConstraint` applicati come attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-912">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="26008-913">È possibile usare il modello di applicazione per applicare i vincoli e questo è probabilmente l'approccio più flessibile, poiché consente di metaprogrammare le modalità di applicazione.</span><span class="sxs-lookup"><span data-stu-id="26008-913">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="26008-914">Nell'esempio seguente un vincolo sceglie un'azione in base a un *codice paese* dei dati della route.</span><span class="sxs-lookup"><span data-stu-id="26008-914">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="26008-915">Vedere l'[esempio completo su GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span><span class="sxs-lookup"><span data-stu-id="26008-915">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

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

<span data-ttu-id="26008-916">L'utente è responsabile dell'implementazione del metodo `Accept` e della scelta di un "ordine" per il vincolo da eseguire.</span><span class="sxs-lookup"><span data-stu-id="26008-916">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="26008-917">In questo caso il metodo `Accept` restituisce `true` per indicare che l'azione è una corrispondenza quando il valore di route `country` corrisponde.</span><span class="sxs-lookup"><span data-stu-id="26008-917">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="26008-918">Questo comportamento è diverso da quello di `RouteValueAttribute` poiché viene consentito il fallback a un'azione senza attributi.</span><span class="sxs-lookup"><span data-stu-id="26008-918">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="26008-919">L'esempio indica che se si definisce un'azione `en-US`, per il codice paese, ad esempio `fr-FR`, viene eseguito il fallback a un controller più generico a cui non è applicato `[CountrySpecific(...)]`.</span><span class="sxs-lookup"><span data-stu-id="26008-919">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="26008-920">La proprietà `Order` decide a quale *fase* appartiene il vincolo.</span><span class="sxs-lookup"><span data-stu-id="26008-920">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="26008-921">I vincoli relativi alle azioni vengono eseguiti in gruppi basati su `Order`.</span><span class="sxs-lookup"><span data-stu-id="26008-921">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="26008-922">Ad esempio, tutti gli attributi del metodo HTTP specificati dal framework usano lo stesso valore `Order` in modo da essere eseguiti nella stessa fase.</span><span class="sxs-lookup"><span data-stu-id="26008-922">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="26008-923">È possibile usare un numero illimitato di fasi per implementare i criteri necessari.</span><span class="sxs-lookup"><span data-stu-id="26008-923">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="26008-924">Quando si sceglie un valore per `Order` considerare se il vincolo dovrà essere applicato o meno prima dei metodi HTTP.</span><span class="sxs-lookup"><span data-stu-id="26008-924">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="26008-925">I numeri più bassi vengono eseguiti per primi.</span><span class="sxs-lookup"><span data-stu-id="26008-925">Lower numbers run first.</span></span>

::: moniker-end
