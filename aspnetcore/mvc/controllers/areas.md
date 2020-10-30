---
title: Aree in ASP.NET Core
author: rick-anderson
description: Informazioni sulle aree, una funzionalità di ASP.NET MVC che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi separato (per il routing) e struttura di cartelle (per le visualizzazioni).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053527"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="d5b7e-103">Aree in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5b7e-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="d5b7e-104">Di [e](https://twitter.com/debug_mode) [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5b7e-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5b7e-105">Le aree sono una funzionalità di ASP.NET usata per organizzare le funzionalità correlate in un gruppo come separato:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="d5b7e-106">Spazio dei nomi per il routing.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-106">Namespace for routing.</span></span>
* <span data-ttu-id="d5b7e-107">Struttura di cartelle per le visualizzazioni e le :::no-loc(Razor)::: pagine.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="d5b7e-108">L'uso delle aree crea una gerarchia ai fini del routing aggiungendo un altro parametro di route, `area` , a `controller` e `action` o a una :::no-loc(Razor)::: pagina `page` .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="d5b7e-109">Le aree consentono di partizionare un ASP.NET Core app Web in gruppi funzionali più piccoli, ognuno con un proprio set di :::no-loc(Razor)::: pagine, controller, visualizzazioni e modelli.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="d5b7e-110">Un'area è in effetti una struttura all'interno di un'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="d5b7e-111">In un progetto Web ASP.NET Core i componenti logici come pagine, modello, controller e visualizzazione si trovano in cartelle diverse.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="d5b7e-112">Il runtime di ASP.NET Core crea una relazione tra questi componenti usando convenzioni di denominazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d5b7e-113">Per un'app di grandi dimensioni può risultare utile suddividere l'app in aree di funzionalità di alto livello distinte.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="d5b7e-114">È il caso, ad esempio, di un'app di e-commerce con più business unit, ad esempio per il completamento della transazione, la fatturazione e la ricerca.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="d5b7e-115">Ognuna di queste unità dispone di una propria area per contenere viste, controller, :::no-loc(Razor)::: pagine e modelli.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="d5b7e-116">In un progetto è consigliabile usare le aree quando:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="d5b7e-117">L'app è costituita da più componenti funzionali di alto livello che possono rimanere logicamente separati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="d5b7e-118">Si vuole suddividere l'app in modo da poter lavorare su ogni area funzionale in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="d5b7e-119">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d5b7e-120">Il download di esempio fornisce un'app di base per testare le aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="d5b7e-121">Se si usano :::no-loc(Razor)::: le pagine, vedere [aree con :::no-loc(Razor)::: pagine](#areas-with-razor-pages) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="d5b7e-122">Aree per i controller con visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-122">Areas for controllers with views</span></span>

<span data-ttu-id="d5b7e-123">Una tipica app Web ASP.NET Core che usa aree, controller e visualizzazioni contiene quanto segue:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="d5b7e-124">Una [struttura di cartelle dell'area](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="d5b7e-125">Controller con l' [`[Area]`](#attribute) attributo per associare il controller all'area:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="d5b7e-126">La [route di area aggiunta all'avvio](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="d5b7e-127">Struttura di cartelle dell'area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-127">Area folder structure</span></span>

<span data-ttu-id="d5b7e-128">Si consideri un'applicazione che ha due gruppi logici, *Prodotti* e *Servizi* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="d5b7e-129">Usando le aree, la struttura delle cartelle sarebbe simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="d5b7e-130">Project name (Nome progetto)</span><span class="sxs-lookup"><span data-stu-id="d5b7e-130">Project name</span></span>
  * <span data-ttu-id="d5b7e-131">Aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-131">Areas</span></span>
    * <span data-ttu-id="d5b7e-132">Prodotti</span><span class="sxs-lookup"><span data-stu-id="d5b7e-132">Products</span></span>
      * <span data-ttu-id="d5b7e-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="d5b7e-133">Controllers</span></span>
        * <span data-ttu-id="d5b7e-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-134">HomeController.cs</span></span>
        * <span data-ttu-id="d5b7e-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-135">ManageController.cs</span></span>
      * <span data-ttu-id="d5b7e-136">Viste</span><span class="sxs-lookup"><span data-stu-id="d5b7e-136">Views</span></span>
        * <span data-ttu-id="d5b7e-137">Home page</span><span class="sxs-lookup"><span data-stu-id="d5b7e-137">Home</span></span>
          * <span data-ttu-id="d5b7e-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-138">Index.cshtml</span></span>
        * <span data-ttu-id="d5b7e-139">Gestione</span><span class="sxs-lookup"><span data-stu-id="d5b7e-139">Manage</span></span>
          * <span data-ttu-id="d5b7e-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-140">Index.cshtml</span></span>
          * <span data-ttu-id="d5b7e-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-141">About.cshtml</span></span>
    * <span data-ttu-id="d5b7e-142">Servizi</span><span class="sxs-lookup"><span data-stu-id="d5b7e-142">Services</span></span>
      * <span data-ttu-id="d5b7e-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="d5b7e-143">Controllers</span></span>
        * <span data-ttu-id="d5b7e-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-144">HomeController.cs</span></span>
      * <span data-ttu-id="d5b7e-145">Viste</span><span class="sxs-lookup"><span data-stu-id="d5b7e-145">Views</span></span>
        * <span data-ttu-id="d5b7e-146">Home page</span><span class="sxs-lookup"><span data-stu-id="d5b7e-146">Home</span></span>
          * <span data-ttu-id="d5b7e-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-147">Index.cshtml</span></span>

<span data-ttu-id="d5b7e-148">Anche se il layout precedente è tipico quando si usano le aree, per usare questa struttura di cartelle sono necessari solo i file delle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="d5b7e-149">L'individuazione delle visualizzazioni cercherà un file di visualizzazione area corrispondente in quest'ordine:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="d5b7e-150">Associare il controller a un'area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-150">Associate the controller with an Area</span></span>

<span data-ttu-id="d5b7e-151">I controller di area sono designati con l'attributo [ &lbrack; area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="d5b7e-152">Aggiungere una route di area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-152">Add Area route</span></span>

<span data-ttu-id="d5b7e-153">Le route di area usano in genere il  [routing convenzionale](xref:mvc/controllers/routing#cr) anziché il [routing degli attributi](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="d5b7e-154">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d5b7e-155">In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d5b7e-156">`{area:...}` può essere usato come token nei modelli di route se lo spazio URL è uniforme in tutte le aree:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="d5b7e-157">Nel codice precedente, `exists` applica il vincolo che la route deve corrispondere a un'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="d5b7e-158">Utilizzo `{area:...}` di con `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="d5b7e-159">È il meccanismo meno complicato per aggiungere il routing alle aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="d5b7e-160">Corrisponde a tutti i controller con l' `[Area("Area name")]` attributo.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="d5b7e-161">Il codice seguente usa <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> per creare due route di area denominate:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="d5b7e-162">Per altre informazioni, vedere [Aree](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="d5b7e-163">Generazione di collegamenti con le aree MVC</span><span class="sxs-lookup"><span data-stu-id="d5b7e-163">Link generation with MVC areas</span></span>

<span data-ttu-id="d5b7e-164">Il codice seguente dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) mostra la generazione di collegamenti con l'area specificata:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d5b7e-165">Il download di esempio include una [visualizzazione parziale](xref:mvc/views/partial) che contiene:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="d5b7e-166">Collegamenti precedenti.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-166">The preceding links.</span></span>
* <span data-ttu-id="d5b7e-167">Collegamenti simili ai precedenti eccetto `area` non specificati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="d5b7e-168">La visualizzazione parziale viene referenziata nel [file di layout](xref:mvc/views/layout), in modo che ogni pagina nell'app mostri i collegamenti generati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d5b7e-169">I collegamenti generati senza specificare l'area sono validi solo quando sono referenziati da una pagina nella stessa area e controller.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="d5b7e-170">Quando l'area o il controller non sono specificati, il routing dipende dai valori di [ambiente](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="d5b7e-171">I valori di route correnti della richiesta corrente sono considerati valori di ambiente per la generazione del collegamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d5b7e-172">In molti casi per l'app di esempio, l'uso dei valori di ambiente genera collegamenti non corretti con il markup che non specifica l'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="d5b7e-173">Per altre informazioni, vedere [Routing ad azioni del controller](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="d5b7e-174">Layout condiviso per le aree tramite il file _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="d5b7e-175">Per condividere un layout comune per l'intera app, Mantieni il *_ViewStart. cshtml* nella [cartella radice dell'applicazione](#arf).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="d5b7e-176">Per altre informazioni, vedere <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="d5b7e-177">Cartella radice dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="d5b7e-177">Application root folder</span></span>

<span data-ttu-id="d5b7e-178">La cartella radice dell'applicazione è la cartella che contiene *Startup.cs* nell'app Web creata con i modelli ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="d5b7e-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="d5b7e-180">*/Views/_ViewImports. cshtml* , per MVC e */pages/_ViewImports. cshtml* per le :::no-loc(Razor)::: pagine, non viene importato nelle viste nelle aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="d5b7e-181">Usare uno degli approcci seguenti per fornire le importazioni di visualizzazione a tutte le visualizzazioni:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="d5b7e-182">Aggiungere *_ViewImports. cshtml* alla [cartella radice dell'applicazione](#arf).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="d5b7e-183">Un *_ViewImports. cshtml* nella cartella radice dell'applicazione si applica a tutte le visualizzazioni nell'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="d5b7e-184">Copiare il file *_ViewImports. cshtml* nella cartella della visualizzazione appropriata in aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="d5b7e-185">Il file *_ViewImports. cshtml* contiene in genere gli [Helper Tag](xref:mvc/views/tag-helpers/intro) che importano `@using` istruzioni, e `@inject` .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="d5b7e-186">Per altre informazioni, vedere [importazione di direttive condivise](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="d5b7e-187">Modificare la cartella dell'area predefinita in cui sono archiviate le visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="d5b7e-188">Il codice seguente modifica la cartella dell'area predefinita da `"Areas"` a `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="d5b7e-189">Aree con :::no-loc(Razor)::: pagine</span><span class="sxs-lookup"><span data-stu-id="d5b7e-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="d5b7e-190">Le aree con :::no-loc(Razor)::: pagine richiedono una `Areas/<area name>/Pages` cartella nella radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="d5b7e-191">La struttura di cartelle seguente viene usata con l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="d5b7e-192">Project name (Nome progetto)</span><span class="sxs-lookup"><span data-stu-id="d5b7e-192">Project name</span></span>
  * <span data-ttu-id="d5b7e-193">Aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-193">Areas</span></span>
    * <span data-ttu-id="d5b7e-194">Prodotti</span><span class="sxs-lookup"><span data-stu-id="d5b7e-194">Products</span></span>
      * <span data-ttu-id="d5b7e-195">.NET</span><span class="sxs-lookup"><span data-stu-id="d5b7e-195">Pages</span></span>
        * <span data-ttu-id="d5b7e-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="d5b7e-196">_ViewImports</span></span>
        * <span data-ttu-id="d5b7e-197">Informazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-197">About</span></span>
        * <span data-ttu-id="d5b7e-198">Indice</span><span class="sxs-lookup"><span data-stu-id="d5b7e-198">Index</span></span>
    * <span data-ttu-id="d5b7e-199">Servizi</span><span class="sxs-lookup"><span data-stu-id="d5b7e-199">Services</span></span>
      * <span data-ttu-id="d5b7e-200">.NET</span><span class="sxs-lookup"><span data-stu-id="d5b7e-200">Pages</span></span>
        * <span data-ttu-id="d5b7e-201">Gestione</span><span class="sxs-lookup"><span data-stu-id="d5b7e-201">Manage</span></span>
          * <span data-ttu-id="d5b7e-202">Informazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-202">About</span></span>
          * <span data-ttu-id="d5b7e-203">Indice</span><span class="sxs-lookup"><span data-stu-id="d5b7e-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="d5b7e-204">Generazione di collegamenti con :::no-loc(Razor)::: pagine e aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="d5b7e-205">Il codice seguente dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra la generazione di collegamenti con l'area specificata (ad esempio, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d5b7e-206">Il download di esempio include una [visualizzazione parziale](xref:mvc/views/partial) che contiene i collegamenti precedenti e gli stessi collegamenti senza specificare l'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d5b7e-207">La visualizzazione parziale viene referenziata nel [file di layout](xref:mvc/views/layout), in modo che ogni pagina nell'app mostri i collegamenti generati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d5b7e-208">I collegamenti generati senza specificare l'area sono validi solo in caso di riferimento da una pagina nella stessa area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="d5b7e-209">Quando l'area non è specificata, il routing dipende dai valori di *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d5b7e-210">I valori di route correnti della richiesta corrente sono considerati valori di ambiente per la generazione del collegamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d5b7e-211">In molti casi per l'app di esempio, l'uso dei valori di ambiente genera collegamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="d5b7e-212">Si considerino, ad esempio, i collegamenti generati dal codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="d5b7e-213">Per il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-213">For the preceding code:</span></span>

* <span data-ttu-id="d5b7e-214">Il collegamento generato da `<a asp-page="/Manage/About">` è corretto solo quando l'ultima richiesta riguarda una pagina nell'area `Services`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="d5b7e-215">Ad esempio `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="d5b7e-216">Il collegamento generato da `<a asp-page="/About">` è corretto solo quando l'ultima richiesta riguarda una pagina in `/Home`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="d5b7e-217">Il codice deriva dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="d5b7e-218">Importare lo spazio dei nomi e gli helper tag con il file _ViewImports</span><span class="sxs-lookup"><span data-stu-id="d5b7e-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="d5b7e-219">È possibile aggiungere un file *_ViewImports. cshtml* a ogni cartella di *pagine* dell'area per importare lo spazio dei nomi e gli helper tag in ogni :::no-loc(Razor)::: pagina della cartella.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="d5b7e-220">Prendere in considerazione l'area *Services* del codice di esempio, che non contiene un file *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d5b7e-221">Il markup seguente mostra la pagina */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="d5b7e-222">Nel markup precedente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-222">In the preceding markup:</span></span>

* <span data-ttu-id="d5b7e-223">Il nome di dominio completo deve essere usato per specificare il modello (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="d5b7e-224">[Gli helper tag](xref:mvc/views/tag-helpers/intro) sono abilitati da `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="d5b7e-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="d5b7e-225">Nel download di esempio, l'area Products contiene il file *_ViewImports.cshtml* seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="d5b7e-226">Il markup seguente mostra la pagina */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="d5b7e-227">Nel file precedente lo spazio dei nomi e la direttiva `@addTagHelper` vengono importati dal file *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="d5b7e-228">Per altre informazioni, vedere [Gestione dell'ambito dell'helper tag](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importazione delle direttive condivise](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="d5b7e-229">Layout condiviso per le :::no-loc(Razor)::: aree delle pagine</span><span class="sxs-lookup"><span data-stu-id="d5b7e-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="d5b7e-230">Per condividere un layout comune per l'intera app, spostare *_ViewStart.cshtml* nella cartella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="d5b7e-231">Pubblicazione di aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-231">Publishing Areas</span></span>

<span data-ttu-id="d5b7e-232">Tutti i file \*.cshtml e i file all'interno della directory *wwwroot* vengono pubblicati nell'output quando `<Project Sdk="Microsoft.NET.Sdk.Web">` è incluso nel file \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5b7e-233">Le aree sono una funzionalità di ASP.NET che consente di organizzare le funzioni correlate in un gruppo come spazio dei nomi separato (per il routing) e struttura di cartelle (per le visualizzazioni).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="d5b7e-234">L'uso delle aree crea una gerarchia ai fini del routing aggiungendo un altro parametro di route, `area` , a `controller` e `action` o a una :::no-loc(Razor)::: pagina `page` .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="d5b7e-235">Le aree consentono di partizionare un ASP.NET Core app Web in gruppi funzionali più piccoli, ognuno con un proprio set di :::no-loc(Razor)::: pagine, controller, visualizzazioni e modelli.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="d5b7e-236">Un'area è in effetti una struttura all'interno di un'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="d5b7e-237">In un progetto Web ASP.NET Core i componenti logici come pagine, modello, controller e visualizzazione si trovano in cartelle diverse.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="d5b7e-238">Il runtime di ASP.NET Core crea una relazione tra questi componenti usando convenzioni di denominazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d5b7e-239">Per un'app di grandi dimensioni può risultare utile suddividere l'app in aree di funzionalità di alto livello distinte.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="d5b7e-240">È il caso, ad esempio, di un'app di e-commerce con più business unit, ad esempio per il completamento della transazione, la fatturazione e la ricerca.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="d5b7e-241">Ognuna di queste unità dispone di una propria area per contenere viste, controller, :::no-loc(Razor)::: pagine e modelli.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="d5b7e-242">In un progetto è consigliabile usare le aree quando:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="d5b7e-243">L'app è costituita da più componenti funzionali di alto livello che possono rimanere logicamente separati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="d5b7e-244">Si vuole suddividere l'app in modo da poter lavorare su ogni area funzionale in modo indipendente.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="d5b7e-245">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d5b7e-246">Il download di esempio fornisce un'app di base per testare le aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="d5b7e-247">Se si usano :::no-loc(Razor)::: le pagine, vedere [aree con :::no-loc(Razor)::: pagine](#areas-with-razor-pages) in questo documento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="d5b7e-248">Aree per i controller con visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-248">Areas for controllers with views</span></span>

<span data-ttu-id="d5b7e-249">Una tipica app Web ASP.NET Core che usa aree, controller e visualizzazioni contiene quanto segue:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="d5b7e-250">Una [struttura di cartelle dell'area](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="d5b7e-251">Controller con l' [`[Area]`](#attribute) attributo per associare il controller all'area:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="d5b7e-252">La [route di area aggiunta all'avvio](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="d5b7e-253">Struttura di cartelle dell'area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-253">Area folder structure</span></span>

<span data-ttu-id="d5b7e-254">Si consideri un'applicazione che ha due gruppi logici, *Prodotti* e *Servizi* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="d5b7e-255">Usando le aree, la struttura delle cartelle sarebbe simile alla seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="d5b7e-256">Project name (Nome progetto)</span><span class="sxs-lookup"><span data-stu-id="d5b7e-256">Project name</span></span>
  * <span data-ttu-id="d5b7e-257">Aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-257">Areas</span></span>
    * <span data-ttu-id="d5b7e-258">Prodotti</span><span class="sxs-lookup"><span data-stu-id="d5b7e-258">Products</span></span>
      * <span data-ttu-id="d5b7e-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="d5b7e-259">Controllers</span></span>
        * <span data-ttu-id="d5b7e-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-260">HomeController.cs</span></span>
        * <span data-ttu-id="d5b7e-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-261">ManageController.cs</span></span>
      * <span data-ttu-id="d5b7e-262">Viste</span><span class="sxs-lookup"><span data-stu-id="d5b7e-262">Views</span></span>
        * <span data-ttu-id="d5b7e-263">Home page</span><span class="sxs-lookup"><span data-stu-id="d5b7e-263">Home</span></span>
          * <span data-ttu-id="d5b7e-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-264">Index.cshtml</span></span>
        * <span data-ttu-id="d5b7e-265">Gestione</span><span class="sxs-lookup"><span data-stu-id="d5b7e-265">Manage</span></span>
          * <span data-ttu-id="d5b7e-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-266">Index.cshtml</span></span>
          * <span data-ttu-id="d5b7e-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-267">About.cshtml</span></span>
    * <span data-ttu-id="d5b7e-268">Servizi</span><span class="sxs-lookup"><span data-stu-id="d5b7e-268">Services</span></span>
      * <span data-ttu-id="d5b7e-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="d5b7e-269">Controllers</span></span>
        * <span data-ttu-id="d5b7e-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d5b7e-270">HomeController.cs</span></span>
      * <span data-ttu-id="d5b7e-271">Viste</span><span class="sxs-lookup"><span data-stu-id="d5b7e-271">Views</span></span>
        * <span data-ttu-id="d5b7e-272">Home page</span><span class="sxs-lookup"><span data-stu-id="d5b7e-272">Home</span></span>
          * <span data-ttu-id="d5b7e-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-273">Index.cshtml</span></span>

<span data-ttu-id="d5b7e-274">Anche se il layout precedente è tipico quando si usano le aree, per usare questa struttura di cartelle sono necessari solo i file delle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="d5b7e-275">L'individuazione delle visualizzazioni cercherà un file di visualizzazione area corrispondente in quest'ordine:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="d5b7e-276">Associare il controller a un'area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-276">Associate the controller with an Area</span></span>

<span data-ttu-id="d5b7e-277">I controller di area sono designati con l'attributo [ &lbrack; area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="d5b7e-278">Aggiungere una route di area</span><span class="sxs-lookup"><span data-stu-id="d5b7e-278">Add Area route</span></span>

<span data-ttu-id="d5b7e-279">Le route di area usano in genere il routing convenzionale anziché il routing con attributi.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="d5b7e-280">Il routing convenzionale dipende dall'ordinamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="d5b7e-281">In generale, le route con aree devono essere posizionate prima delle altre nella tabella di route poiché sono più specifiche rispetto alle route senza un'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="d5b7e-282">`{area:...}` può essere usato come token nei modelli di route se lo spazio URL è uniforme in tutte le aree:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="d5b7e-283">Nel codice precedente, `exists` applica il vincolo che la route deve corrispondere a un'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="d5b7e-284">L'uso di `{area:...}` è il meccanismo meno complesso per l'aggiunta del routing alle aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="d5b7e-285">Il codice seguente usa <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> per creare due route di area denominate:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="d5b7e-286">Quando si usa `MapAreaRoute` con ASP.NET Core 2.2, vedere [questo problema su GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="d5b7e-287">Per altre informazioni, vedere [Aree](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="d5b7e-288">Generazione di collegamenti con le aree MVC</span><span class="sxs-lookup"><span data-stu-id="d5b7e-288">Link generation with MVC areas</span></span>

<span data-ttu-id="d5b7e-289">Il codice seguente dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) mostra la generazione di collegamenti con l'area specificata:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d5b7e-290">I collegamenti generati con il codice precedente sono validi ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d5b7e-291">Il download di esempio include una [visualizzazione parziale](xref:mvc/views/partial) che contiene i collegamenti precedenti e gli stessi collegamenti senza specificare l'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d5b7e-292">La visualizzazione parziale viene referenziata nel [file di layout](xref:mvc/views/layout), in modo che ogni pagina nell'app mostri i collegamenti generati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d5b7e-293">I collegamenti generati senza specificare l'area sono validi solo quando sono referenziati da una pagina nella stessa area e controller.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="d5b7e-294">Quando l'area o il controller non sono specificati, il routing dipende dai valori di *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d5b7e-295">I valori di route correnti della richiesta corrente sono considerati valori di ambiente per la generazione del collegamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d5b7e-296">In molti casi per l'app di esempio, l'uso dei valori di ambiente genera collegamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="d5b7e-297">Per altre informazioni, vedere [Routing ad azioni del controller](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="d5b7e-298">Layout condiviso per le aree tramite il file _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="d5b7e-299">Per condividere un layout comune per l'intera app, spostare *_ViewStart.cshtml* nella cartella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="d5b7e-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="d5b7e-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="d5b7e-301">Nella posizione standard */Views/_ViewImports.cshtml* non si applica alle aree.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="d5b7e-302">Per usare gli [Helper Tag](xref:mvc/views/tag-helpers/intro)comuni, `@using` o `@inject` nella propria area, assicurarsi che sia presente un file *_ViewImports. cshtml* appropriato [per le visualizzazioni area](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="d5b7e-303">Se si vuole lo stesso comportamento in tutte le visualizzazioni, spostare */Views/_ViewImports.cshtml* nella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="d5b7e-304">Modificare la cartella dell'area predefinita in cui sono archiviate le visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="d5b7e-305">Il codice seguente modifica la cartella dell'area predefinita da `"Areas"` a `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="d5b7e-306">Aree con :::no-loc(Razor)::: pagine</span><span class="sxs-lookup"><span data-stu-id="d5b7e-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="d5b7e-307">Le aree con :::no-loc(Razor)::: pagine richiedono una `Areas/<area name>/Pages` cartella nella radice dell'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="d5b7e-308">La struttura di cartelle seguente viene usata con l'[app di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="d5b7e-309">Project name (Nome progetto)</span><span class="sxs-lookup"><span data-stu-id="d5b7e-309">Project name</span></span>
  * <span data-ttu-id="d5b7e-310">Aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-310">Areas</span></span>
    * <span data-ttu-id="d5b7e-311">Prodotti</span><span class="sxs-lookup"><span data-stu-id="d5b7e-311">Products</span></span>
      * <span data-ttu-id="d5b7e-312">.NET</span><span class="sxs-lookup"><span data-stu-id="d5b7e-312">Pages</span></span>
        * <span data-ttu-id="d5b7e-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="d5b7e-313">_ViewImports</span></span>
        * <span data-ttu-id="d5b7e-314">Informazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-314">About</span></span>
        * <span data-ttu-id="d5b7e-315">Indice</span><span class="sxs-lookup"><span data-stu-id="d5b7e-315">Index</span></span>
    * <span data-ttu-id="d5b7e-316">Servizi</span><span class="sxs-lookup"><span data-stu-id="d5b7e-316">Services</span></span>
      * <span data-ttu-id="d5b7e-317">.NET</span><span class="sxs-lookup"><span data-stu-id="d5b7e-317">Pages</span></span>
        * <span data-ttu-id="d5b7e-318">Gestione</span><span class="sxs-lookup"><span data-stu-id="d5b7e-318">Manage</span></span>
          * <span data-ttu-id="d5b7e-319">Informazioni</span><span class="sxs-lookup"><span data-stu-id="d5b7e-319">About</span></span>
          * <span data-ttu-id="d5b7e-320">Indice</span><span class="sxs-lookup"><span data-stu-id="d5b7e-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="d5b7e-321">Generazione di collegamenti con :::no-loc(Razor)::: pagine e aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="d5b7e-322">Il codice seguente dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) mostra la generazione di collegamenti con l'area specificata (ad esempio, `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="d5b7e-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="d5b7e-323">I collegamenti generati con il codice precedente sono validi ovunque nell'app.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="d5b7e-324">Il download di esempio include una [visualizzazione parziale](xref:mvc/views/partial) che contiene i collegamenti precedenti e gli stessi collegamenti senza specificare l'area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="d5b7e-325">La visualizzazione parziale viene referenziata nel [file di layout](xref:mvc/views/layout), in modo che ogni pagina nell'app mostri i collegamenti generati.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="d5b7e-326">I collegamenti generati senza specificare l'area sono validi solo in caso di riferimento da una pagina nella stessa area.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="d5b7e-327">Quando l'area non è specificata, il routing dipende dai valori di *ambiente* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="d5b7e-328">I valori di route correnti della richiesta corrente sono considerati valori di ambiente per la generazione del collegamento.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="d5b7e-329">In molti casi per l'app di esempio, l'uso dei valori di ambiente genera collegamenti non corretti.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="d5b7e-330">Si considerino, ad esempio, i collegamenti generati dal codice seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="d5b7e-331">Per il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-331">For the preceding code:</span></span>

* <span data-ttu-id="d5b7e-332">Il collegamento generato da `<a asp-page="/Manage/About">` è corretto solo quando l'ultima richiesta riguarda una pagina nell'area `Services`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="d5b7e-333">Ad esempio `/Services/Manage/`, `/Services/Manage/Index` o `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="d5b7e-334">Il collegamento generato da `<a asp-page="/About">` è corretto solo quando l'ultima richiesta riguarda una pagina in `/Home`.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="d5b7e-335">Il codice deriva dal [download di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="d5b7e-336">Importare lo spazio dei nomi e gli helper tag con il file _ViewImports</span><span class="sxs-lookup"><span data-stu-id="d5b7e-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="d5b7e-337">È possibile aggiungere un file *_ViewImports. cshtml* a ogni cartella di *pagine* dell'area per importare lo spazio dei nomi e gli helper tag in ogni :::no-loc(Razor)::: pagina della cartella.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="d5b7e-338">Prendere in considerazione l'area *Services* del codice di esempio, che non contiene un file *_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="d5b7e-339">Il markup seguente mostra la pagina */Services/Manage/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="d5b7e-340">Nel markup precedente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-340">In the preceding markup:</span></span>

* <span data-ttu-id="d5b7e-341">Il nome di dominio completo deve essere usato per specificare il modello (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="d5b7e-342">[Gli helper tag](xref:mvc/views/tag-helpers/intro) sono abilitati da `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="d5b7e-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="d5b7e-343">Nel download di esempio, l'area Products contiene il file *_ViewImports.cshtml* seguente:</span><span class="sxs-lookup"><span data-stu-id="d5b7e-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="d5b7e-344">Il markup seguente mostra la pagina */Products/about* :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="d5b7e-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="d5b7e-345">Nel file precedente lo spazio dei nomi e la direttiva `@addTagHelper` vengono importati dal file *Areas/Products/Pages/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d5b7e-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="d5b7e-346">Per altre informazioni, vedere [Gestione dell'ambito dell'helper tag](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) e [Importazione delle direttive condivise](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="d5b7e-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="d5b7e-347">Layout condiviso per le :::no-loc(Razor)::: aree delle pagine</span><span class="sxs-lookup"><span data-stu-id="d5b7e-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="d5b7e-348">Per condividere un layout comune per l'intera app, spostare *_ViewStart.cshtml* nella cartella radice dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="d5b7e-349">Pubblicazione di aree</span><span class="sxs-lookup"><span data-stu-id="d5b7e-349">Publishing Areas</span></span>

<span data-ttu-id="d5b7e-350">Tutti i file \*.cshtml e i file all'interno della directory *wwwroot* vengono pubblicati nell'output quando `<Project Sdk="Microsoft.NET.Sdk.Web">` è incluso nel file \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="d5b7e-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
