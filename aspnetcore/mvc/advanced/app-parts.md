---
title: Condividi controller, visualizzazioni, Razor pagine e altro ancora con le parti dell'applicazione in ASP.NET Core
author: rick-anderson
description: Condividi controller, Visualizza, Razor pagine e altro con le parti dell'applicazione in ASP.NET Core
ms.author: riande
ms.date: 11/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 68991a3df5e09b63dc52bdadae55f055a721ad3c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774405"
---
# <a name="share-controllers-views-razor-pages-and-more-with-application-parts"></a><span data-ttu-id="81b28-103">Condividi controller, visualizzazioni, Razor pagine e altro ancora con le parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="81b28-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81b28-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81b28-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81b28-105">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81b28-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="81b28-106">Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="81b28-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="81b28-107">Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper Razor tag, pagine, origini di compilazione Razor e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="81b28-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="81b28-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>è una parte dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="81b28-109">`AssemblyPart`Incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="81b28-110">I [provider di funzionalità](#fp) funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81b28-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="81b28-111">Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="81b28-112">Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app.</span><span class="sxs-lookup"><span data-stu-id="81b28-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="81b28-113">Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni Razor , pagine, origini di compilazione Razor, helper tag e altro ancora con più app.</span><span class="sxs-lookup"><span data-stu-id="81b28-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="81b28-114">La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.</span><span class="sxs-lookup"><span data-stu-id="81b28-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="81b28-115">ASP.NET Core le app caricano <xref:System.Web.WebPages.ApplicationPart>le funzionalità da.</span><span class="sxs-lookup"><span data-stu-id="81b28-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="81b28-116">La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe rappresenta una parte dell'applicazione supportata da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="81b28-117">Carica ASP.NET Core funzionalità</span><span class="sxs-lookup"><span data-stu-id="81b28-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="81b28-118">Usare le <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> classi <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> e per individuare e caricare ASP.NET Core funzionalità (controller, componenti di visualizzazione e così via).</span><span class="sxs-lookup"><span data-stu-id="81b28-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="81b28-119">Consente <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> di tenere traccia delle parti dell'applicazione e dei provider di funzionalità disponibili.</span><span class="sxs-lookup"><span data-stu-id="81b28-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="81b28-120">`ApplicationPartManager`è configurato in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="81b28-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="81b28-121">Il codice seguente offre un approccio alternativo alla configurazione `ApplicationPartManager` di `AssemblyPart`utilizzando:</span><span class="sxs-lookup"><span data-stu-id="81b28-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="81b28-122">I due esempi `SharedController` di codice precedenti caricano da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="81b28-123">L' `SharedController` oggetto non è presente nel progetto dell'app.</span><span class="sxs-lookup"><span data-stu-id="81b28-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="81b28-124">Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="81b28-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="81b28-125">Includi visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="81b28-125">Include views</span></span>

<span data-ttu-id="81b28-126">Utilizzare una [ Razor libreria di classi](xref:razor-pages/ui-class) per includere viste nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="81b28-127">Impedisci il caricamento delle risorse</span><span class="sxs-lookup"><span data-stu-id="81b28-127">Prevent loading resources</span></span>

<span data-ttu-id="81b28-128">Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="81b28-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="81b28-129">Aggiungere o rimuovere membri della <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> raccolta per nascondere o rendere disponibili risorse.</span><span class="sxs-lookup"><span data-stu-id="81b28-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="81b28-130">L'ordine delle voci nella raccolta `ApplicationParts` non è importante.</span><span class="sxs-lookup"><span data-stu-id="81b28-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="81b28-131">Configurare prima `ApplicationPartManager` di usarlo per configurare i servizi nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="81b28-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="81b28-132">Configurare, ad esempio, `ApplicationPartManager` prima di richiamare `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="81b28-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="81b28-133">Chiamare `Remove` sulla `ApplicationParts` raccolta per rimuovere una risorsa.</span><span class="sxs-lookup"><span data-stu-id="81b28-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="81b28-134">Include `ApplicationPartManager` le parti per:</span><span class="sxs-lookup"><span data-stu-id="81b28-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="81b28-135">Assembly dell'app e assembly dipendenti.</span><span class="sxs-lookup"><span data-stu-id="81b28-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="81b28-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="81b28-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="81b28-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="81b28-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="81b28-138">Provider di funzionalità</span><span class="sxs-lookup"><span data-stu-id="81b28-138">Feature providers</span></span>

<span data-ttu-id="81b28-139">I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti.</span><span class="sxs-lookup"><span data-stu-id="81b28-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="81b28-140">Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:</span><span class="sxs-lookup"><span data-stu-id="81b28-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="81b28-141">`internal class`[RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="81b28-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="81b28-142">I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="81b28-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="81b28-143">I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="81b28-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="81b28-144">L'ordine dei provider di funzionalità in `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="81b28-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="81b28-145">I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.</span><span class="sxs-lookup"><span data-stu-id="81b28-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="81b28-146">Visualizzare funzionalità disponibili</span><span class="sxs-lookup"><span data-stu-id="81b28-146">Display available features</span></span>

<span data-ttu-id="81b28-147">Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` inserimento delle [dipendenze](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="81b28-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="81b28-148">L' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:</span><span class="sxs-lookup"><span data-stu-id="81b28-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="81b28-149">Individuazione nelle parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="81b28-149">Discovery in application parts</span></span>

<span data-ttu-id="81b28-150">Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="81b28-151">Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="81b28-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="81b28-152">Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="81b28-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="81b28-153">L' `applicationName` impostazione deve essere impostata sull'assembly radice utilizzato per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="81b28-154">L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.</span><span class="sxs-lookup"><span data-stu-id="81b28-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="81b28-155">L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="81b28-156">Il riferimento può essere diretto o transitivo.</span><span class="sxs-lookup"><span data-stu-id="81b28-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="81b28-157">L'assembly radice deve fare riferimento a Web SDK.</span><span class="sxs-lookup"><span data-stu-id="81b28-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="81b28-158">Il Framework dispone di una logica che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81b28-159">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="81b28-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="81b28-160">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81b28-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="81b28-161">Una *parte dell'applicazione* è un'astrazione sulle risorse di un'app.</span><span class="sxs-lookup"><span data-stu-id="81b28-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="81b28-162">Le parti dell'applicazione consentono ASP.NET Core di individuare controller, componenti di visualizzazione, helper Razor tag, pagine, origini di compilazione Razor e altro ancora.</span><span class="sxs-lookup"><span data-stu-id="81b28-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="81b28-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) è una parte dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="81b28-164">`AssemblyPart`Incapsula un riferimento all'assembly ed espone i tipi e i riferimenti di compilazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="81b28-165">I *provider di funzionalità* funzionano con le parti dell'applicazione per popolare le funzionalità di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81b28-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="81b28-166">Il caso d'uso principale per le parti dell'applicazione consiste nel configurare un'app per individuare (o evitare il caricamento) ASP.NET Core funzionalità da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="81b28-167">Ad esempio, potrebbe essere necessario condividere le funzionalità comuni tra più app.</span><span class="sxs-lookup"><span data-stu-id="81b28-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="81b28-168">Con le parti dell'applicazione è possibile condividere un assembly (DLL) contenente controller, visualizzazioni Razor , pagine, origini di compilazione Razor, helper tag e altro ancora con più app.</span><span class="sxs-lookup"><span data-stu-id="81b28-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="81b28-169">La condivisione di un assembly è preferibile alla duplicazione del codice in più progetti.</span><span class="sxs-lookup"><span data-stu-id="81b28-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="81b28-170">ASP.NET Core le app caricano <xref:System.Web.WebPages.ApplicationPart>le funzionalità da.</span><span class="sxs-lookup"><span data-stu-id="81b28-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="81b28-171">La <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classe rappresenta una parte dell'applicazione supportata da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="81b28-172">Carica ASP.NET Core funzionalità</span><span class="sxs-lookup"><span data-stu-id="81b28-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="81b28-173">Usare le `ApplicationPart` classi `AssemblyPart` e per individuare e caricare ASP.NET Core funzionalità (controller, componenti di visualizzazione e così via).</span><span class="sxs-lookup"><span data-stu-id="81b28-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="81b28-174">Consente <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> di tenere traccia delle parti dell'applicazione e dei provider di funzionalità disponibili.</span><span class="sxs-lookup"><span data-stu-id="81b28-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="81b28-175">`ApplicationPartManager`è configurato in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="81b28-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="81b28-176">Il codice seguente offre un approccio alternativo alla configurazione `ApplicationPartManager` di `AssemblyPart`utilizzando:</span><span class="sxs-lookup"><span data-stu-id="81b28-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="81b28-177">I due esempi `SharedController` di codice precedenti caricano da un assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="81b28-178">L' `SharedController` oggetto non è presente nel progetto dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="81b28-179">Vedere il download dell'esempio di [soluzione WebAppParts](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) .</span><span class="sxs-lookup"><span data-stu-id="81b28-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="81b28-180">Includi visualizzazioni</span><span class="sxs-lookup"><span data-stu-id="81b28-180">Include views</span></span>

<span data-ttu-id="81b28-181">Utilizzare una [ Razor libreria di classi](xref:razor-pages/ui-class) per includere viste nell'assembly.</span><span class="sxs-lookup"><span data-stu-id="81b28-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="81b28-182">Impedisci il caricamento delle risorse</span><span class="sxs-lookup"><span data-stu-id="81b28-182">Prevent loading resources</span></span>

<span data-ttu-id="81b28-183">Le parti dell'applicazione possono essere utilizzate per *evitare* il caricamento di risorse in un assembly o in un percorso specifico.</span><span class="sxs-lookup"><span data-stu-id="81b28-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="81b28-184">Aggiungere o rimuovere membri della <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> raccolta per nascondere o rendere disponibili risorse.</span><span class="sxs-lookup"><span data-stu-id="81b28-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="81b28-185">L'ordine delle voci nella raccolta `ApplicationParts` non è importante.</span><span class="sxs-lookup"><span data-stu-id="81b28-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="81b28-186">Configurare prima `ApplicationPartManager` di usarlo per configurare i servizi nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="81b28-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="81b28-187">Configurare, ad esempio, `ApplicationPartManager` prima di richiamare `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="81b28-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="81b28-188">Chiamare `Remove` sulla `ApplicationParts` raccolta per rimuovere una risorsa.</span><span class="sxs-lookup"><span data-stu-id="81b28-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="81b28-189">Il codice seguente usa <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> per rimuovere `MyDependentLibrary` dall'app:[!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="81b28-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="81b28-190">Include `ApplicationPartManager` le parti per:</span><span class="sxs-lookup"><span data-stu-id="81b28-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="81b28-191">Assembly dell'app e assembly dipendenti.</span><span class="sxs-lookup"><span data-stu-id="81b28-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="81b28-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="81b28-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="81b28-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="81b28-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="81b28-194">Provider di funzionalità dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="81b28-194">Application feature providers</span></span>

<span data-ttu-id="81b28-195">I provider di funzionalità dell'applicazione esaminano le parti dell'applicazione e forniscono funzionalità per tali parti.</span><span class="sxs-lookup"><span data-stu-id="81b28-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="81b28-196">Sono disponibili provider di funzionalità predefiniti per le funzionalità di ASP.NET Core seguenti:</span><span class="sxs-lookup"><span data-stu-id="81b28-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="81b28-197">Controllers</span><span class="sxs-lookup"><span data-stu-id="81b28-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="81b28-198">Helper tag</span><span class="sxs-lookup"><span data-stu-id="81b28-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="81b28-199">Visualizza componenti</span><span class="sxs-lookup"><span data-stu-id="81b28-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="81b28-200">I provider di funzionalità ereditano da <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, dove `T` è il tipo della funzionalità.</span><span class="sxs-lookup"><span data-stu-id="81b28-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="81b28-201">I provider di funzionalità possono essere implementati per uno qualsiasi dei tipi di funzionalità elencati in precedenza.</span><span class="sxs-lookup"><span data-stu-id="81b28-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="81b28-202">L'ordine dei provider di funzionalità in `ApplicationPartManager.FeatureProviders` può influisca sul comportamento in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="81b28-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="81b28-203">I provider aggiunti successivamente possono rispondere alle azioni eseguite dai provider aggiunti precedentemente.</span><span class="sxs-lookup"><span data-stu-id="81b28-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="81b28-204">Visualizzare funzionalità disponibili</span><span class="sxs-lookup"><span data-stu-id="81b28-204">Display available features</span></span>

<span data-ttu-id="81b28-205">Le funzionalità disponibili per un'app possono essere enumerate richiedendo un `ApplicationPartManager` inserimento delle [dipendenze](../../fundamentals/dependency-injection.md):</span><span class="sxs-lookup"><span data-stu-id="81b28-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="81b28-206">L' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) usa il codice precedente per visualizzare le funzionalità dell'app:</span><span class="sxs-lookup"><span data-stu-id="81b28-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

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

## <a name="discovery-in-application-parts"></a><span data-ttu-id="81b28-207">Individuazione nelle parti dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="81b28-207">Discovery in application parts</span></span>

<span data-ttu-id="81b28-208">Gli errori HTTP 404 non sono rari durante lo sviluppo con le parti dell'applicazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="81b28-209">Questi errori sono in genere causati da un requisito essenziale per il modo in cui vengono individuate le parti delle applicazioni.</span><span class="sxs-lookup"><span data-stu-id="81b28-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="81b28-210">Se l'app restituisce un errore HTTP 404, verificare che siano stati soddisfatti i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="81b28-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="81b28-211">L' `applicationName` impostazione deve essere impostata sull'assembly radice utilizzato per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="81b28-212">L'assembly radice utilizzato per l'individuazione è in genere l'assembly del punto di ingresso.</span><span class="sxs-lookup"><span data-stu-id="81b28-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="81b28-213">L'assembly radice deve avere un riferimento alle parti utilizzate per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="81b28-214">Il riferimento può essere diretto o transitivo.</span><span class="sxs-lookup"><span data-stu-id="81b28-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="81b28-215">L'assembly radice deve fare riferimento a Web SDK.</span><span class="sxs-lookup"><span data-stu-id="81b28-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="81b28-216">Il framework ASP.NET Core dispone di una logica di compilazione personalizzata che timbra gli attributi nell'assembly radice utilizzati per l'individuazione.</span><span class="sxs-lookup"><span data-stu-id="81b28-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
