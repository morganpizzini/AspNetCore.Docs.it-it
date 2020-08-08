---
title: Condividi controller, visualizzazioni, Razor pagine e altro ancora con le parti dell'applicazione in ASP.NET Core
author: rick-anderson
description: Condividi controller, Visualizza, Razor pagine e altro con le parti dell'applicazione in ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 690ef0843f567dc2335f4d51436e428207fd6eb1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019573"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="52bc9-103">Condividi controller, visualizzazioni, Razor pagine e altro ancora con le parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="52bc9-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="52bc9-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52bc9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="52bc9-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52bc9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52bc9-106">Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="52bc9-107">Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper tag, Razor pagine, origini di compilazione Razor e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="52bc9-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="52bc9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>è una parte dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="52bc9-109">`AssemblyPart`Incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="52bc9-110">I [provider di funzionalità](#fp) funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52bc9-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="52bc9-111">Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="52bc9-112">Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="52bc9-113">Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni, Razor pagine, origini di compilazione Razor, helper tag e altro ancora con più app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="52bc9-114">La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="52bc9-115">ASP.NET Core le app caricano le funzionalità da <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="52bc9-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="52bc9-116">La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe rappresenta una parte dell'applicazione supportata da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="52bc9-117">Carica ASP.NET Core funzionalità</span><span class="sxs-lookup"><span data-stu-id="52bc9-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="52bc9-118">Usare le <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classi e per individuare e caricare ASP.NET Core funzionalità (controller, componenti di visualizzazione e così via).</span><span class="sxs-lookup"><span data-stu-id="52bc9-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="52bc9-119">Consente di tenere <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> traccia delle parti dell'applicazione e dei provider di funzionalità disponibili.</span><span class="sxs-lookup"><span data-stu-id="52bc9-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="52bc9-120">`ApplicationPartManager`è configurato in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="52bc9-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="52bc9-121">Il codice seguente offre un approccio alternativo alla configurazione di `ApplicationPartManager` utilizzando `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="52bc9-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="52bc9-122">I due esempi di codice precedenti caricano `SharedController` da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="52bc9-123">L'oggetto `SharedController` non è presente nel progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="52bc9-124">Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="52bc9-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="52bc9-125">Includi visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="52bc9-125">Include views</span></span>

<span data-ttu-id="52bc9-126">Utilizzare una [ Razor libreria di classi](xref:razor-pages/ui-class) per includere viste nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="52bc9-127">Impedisci il caricamento delle risorse</span><span class="sxs-lookup"><span data-stu-id="52bc9-127">Prevent loading resources</span></span>

<span data-ttu-id="52bc9-128">Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="52bc9-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="52bc9-129">Aggiungere o rimuovere membri della <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> raccolta per nascondere o rendere disponibili risorse.</span><span class="sxs-lookup"><span data-stu-id="52bc9-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="52bc9-130">L'ordine delle voci nella raccolta `ApplicationParts` non è importante.</span><span class="sxs-lookup"><span data-stu-id="52bc9-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="52bc9-131">Configurare `ApplicationPartManager` prima di usarlo per configurare i servizi nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="52bc9-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="52bc9-132">Configurare, ad esempio, `ApplicationPartManager` prima di richiamare `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="52bc9-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="52bc9-133">Chiamare `Remove` sulla `ApplicationParts` raccolta per rimuovere una risorsa.</span><span class="sxs-lookup"><span data-stu-id="52bc9-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="52bc9-134">`ApplicationPartManager`Include le parti per:</span><span class="sxs-lookup"><span data-stu-id="52bc9-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="52bc9-135">Assembly dell'app e assembly dipendenti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="52bc9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="52bc9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="52bc9-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="52bc9-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="52bc9-138">Provider di funzionalità</span><span class="sxs-lookup"><span data-stu-id="52bc9-138">Feature providers</span></span>

<span data-ttu-id="52bc9-139">I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="52bc9-140">Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:</span><span class="sxs-lookup"><span data-stu-id="52bc9-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="52bc9-141">`internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="52bc9-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="52bc9-142">I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="52bc9-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="52bc9-143">I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="52bc9-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="52bc9-144">L'ordine dei provider di funzionalità in `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="52bc9-145">I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.</span><span class="sxs-lookup"><span data-stu-id="52bc9-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="52bc9-146">Visualizzare funzionalità disponibili</span><span class="sxs-lookup"><span data-stu-id="52bc9-146">Display available features</span></span>

<span data-ttu-id="52bc9-147">Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` inserimento delle [dipendenze](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="52bc9-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="52bc9-148">L' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:</span><span class="sxs-lookup"><span data-stu-id="52bc9-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="52bc9-149">Individuazione nelle parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="52bc9-149">Discovery in application parts</span></span>

<span data-ttu-id="52bc9-150">Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="52bc9-151">Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="52bc9-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="52bc9-152">Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="52bc9-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="52bc9-153">L' `applicationName` impostazione deve essere impostata sull'assembly radice utilizzato per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="52bc9-154">L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.</span><span class="sxs-lookup"><span data-stu-id="52bc9-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="52bc9-155">L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="52bc9-156">Il riferimento può essere diretto o transitivo.</span><span class="sxs-lookup"><span data-stu-id="52bc9-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="52bc9-157">L'assembly radice deve fare riferimento a Web SDK.</span><span class="sxs-lookup"><span data-stu-id="52bc9-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="52bc9-158">Il Framework dispone di una logica che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52bc9-159">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52bc9-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="52bc9-160">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="52bc9-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="52bc9-161">Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="52bc9-162">Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper tag, Razor pagine, origini di compilazione Razor e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="52bc9-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="52bc9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) è una parte dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="52bc9-164">`AssemblyPart`Incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="52bc9-165">I *provider di funzionalità* funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="52bc9-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="52bc9-166">Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="52bc9-167">Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="52bc9-168">Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni, Razor pagine, origini di compilazione Razor, helper tag e altro ancora con più app.</span><span class="sxs-lookup"><span data-stu-id="52bc9-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="52bc9-169">La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="52bc9-170">ASP.NET Core le app caricano le funzionalità da <xref:System.Web.WebPages.ApplicationPart> .</span><span class="sxs-lookup"><span data-stu-id="52bc9-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="52bc9-171">La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe rappresenta una parte dell'applicazione supportata da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="52bc9-172">Carica ASP.NET Core funzionalità</span><span class="sxs-lookup"><span data-stu-id="52bc9-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="52bc9-173">Usare le `ApplicationPart` `AssemblyPart` classi e per individuare e caricare ASP.NET Core funzionalità (controller, componenti di visualizzazione e così via).</span><span class="sxs-lookup"><span data-stu-id="52bc9-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="52bc9-174">Consente di tenere <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> traccia delle parti dell'applicazione e dei provider di funzionalità disponibili.</span><span class="sxs-lookup"><span data-stu-id="52bc9-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="52bc9-175">`ApplicationPartManager`è configurato in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="52bc9-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="52bc9-176">Il codice seguente offre un approccio alternativo alla configurazione di `ApplicationPartManager` utilizzando `AssemblyPart` :</span><span class="sxs-lookup"><span data-stu-id="52bc9-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="52bc9-177">I due esempi di codice precedenti caricano `SharedController` da un assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="52bc9-178">L'oggetto `SharedController` non è presente nel progetto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="52bc9-179">Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="52bc9-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="52bc9-180">Includi visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="52bc9-180">Include views</span></span>

<span data-ttu-id="52bc9-181">Utilizzare una [ Razor libreria di classi](xref:razor-pages/ui-class) per includere viste nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="52bc9-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="52bc9-182">Impedisci il caricamento delle risorse</span><span class="sxs-lookup"><span data-stu-id="52bc9-182">Prevent loading resources</span></span>

<span data-ttu-id="52bc9-183">Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="52bc9-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="52bc9-184">Aggiungere o rimuovere membri della <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> raccolta per nascondere o rendere disponibili risorse.</span><span class="sxs-lookup"><span data-stu-id="52bc9-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="52bc9-185">L'ordine delle voci nella raccolta `ApplicationParts` non è importante.</span><span class="sxs-lookup"><span data-stu-id="52bc9-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="52bc9-186">Configurare `ApplicationPartManager` prima di usarlo per configurare i servizi nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="52bc9-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="52bc9-187">Configurare, ad esempio, `ApplicationPartManager` prima di richiamare `AddControllersAsServices` .</span><span class="sxs-lookup"><span data-stu-id="52bc9-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="52bc9-188">Chiamare `Remove` sulla `ApplicationParts` raccolta per rimuovere una risorsa.</span><span class="sxs-lookup"><span data-stu-id="52bc9-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="52bc9-189">Il codice seguente usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> per rimuovere `MyDependentLibrary` dall'app:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="52bc9-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="52bc9-190">`ApplicationPartManager`Include le parti per:</span><span class="sxs-lookup"><span data-stu-id="52bc9-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="52bc9-191">Assembly dell'app e assembly dipendenti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="52bc9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="52bc9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="52bc9-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="52bc9-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="52bc9-194">Provider di funzionalità dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="52bc9-194">Application feature providers</span></span>

<span data-ttu-id="52bc9-195">I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti.</span><span class="sxs-lookup"><span data-stu-id="52bc9-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="52bc9-196">Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:</span><span class="sxs-lookup"><span data-stu-id="52bc9-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="52bc9-197">Controllers</span><span class="sxs-lookup"><span data-stu-id="52bc9-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="52bc9-198">Helper per i tag</span><span class="sxs-lookup"><span data-stu-id="52bc9-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="52bc9-199">Visualizza componenti</span><span class="sxs-lookup"><span data-stu-id="52bc9-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="52bc9-200">I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="52bc9-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="52bc9-201">I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="52bc9-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="52bc9-202">L'ordine dei provider di funzionalità in `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="52bc9-203">I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.</span><span class="sxs-lookup"><span data-stu-id="52bc9-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="52bc9-204">Visualizzare funzionalità disponibili</span><span class="sxs-lookup"><span data-stu-id="52bc9-204">Display available features</span></span>

<span data-ttu-id="52bc9-205">Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` inserimento delle [dipendenze](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="52bc9-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="52bc9-206">L' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:</span><span class="sxs-lookup"><span data-stu-id="52bc9-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="52bc9-207">Individuazione nelle parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="52bc9-207">Discovery in application parts</span></span>

<span data-ttu-id="52bc9-208">Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="52bc9-209">Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="52bc9-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="52bc9-210">Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="52bc9-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="52bc9-211">L' `applicationName` impostazione deve essere impostata sull'assembly radice utilizzato per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="52bc9-212">L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.</span><span class="sxs-lookup"><span data-stu-id="52bc9-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="52bc9-213">L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="52bc9-214">Il riferimento può essere diretto o transitivo.</span><span class="sxs-lookup"><span data-stu-id="52bc9-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="52bc9-215">L'assembly radice deve fare riferimento a Web SDK.</span><span class="sxs-lookup"><span data-stu-id="52bc9-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="52bc9-216">Il framework ASP.NET Core dispone di una logica di compilazione personalizzata che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="52bc9-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
