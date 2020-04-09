---
title: Bundle e minimifico le risorse statiche in ASP.NET Core
author: scottaddie
description: Informazioni su come ottimizzare le risorse statiche in un'applicazione Web ASP.NET Core applicando tecniche di raggruppamento e minimizzazione.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/17/2019
uid: client-side/bundling-and-minification
ms.openlocfilehash: a7a5c40d6c31c4416212c02c1b491dd794f2a1d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658270"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="2d90b-103">Bundle e minimifico le risorse statiche in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2d90b-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="2d90b-104">Di [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="2d90b-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="2d90b-105">In questo articolo vengono illustrati i vantaggi dell'applicazione di raggruppamento e minimizzazione, incluso il modo in cui queste funzionalità possono essere utilizzate con le app Web di base di ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="2d90b-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="2d90b-106">Che cos'è l'aggregazione e la minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-106">What is bundling and minification</span></span>

<span data-ttu-id="2d90b-107">L'aggregazione e la minimizzazione sono due ottimizzazioni delle prestazioni distinte che è possibile applicare in un'app Web.</span><span class="sxs-lookup"><span data-stu-id="2d90b-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="2d90b-108">Utilizzati insieme, l'aggregazione e la minimizzazione migliorano le prestazioni riducendo il numero di richieste del server e riducendo le dimensioni degli asset statici richiesti.</span><span class="sxs-lookup"><span data-stu-id="2d90b-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="2d90b-109">L'aggregazione e la minimizzazione migliorano principalmente il tempo di caricamento della prima richiesta di pagina.</span><span class="sxs-lookup"><span data-stu-id="2d90b-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="2d90b-110">Una volta richiesta una pagina Web, il browser memorizza nella cache le risorse statiche (JavaScript, CSS e immagini).</span><span class="sxs-lookup"><span data-stu-id="2d90b-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="2d90b-111">Di conseguenza, l'aggregazione e la minimizzazione non migliorano le prestazioni quando si richiede la stessa pagina, o pagine, sullo stesso sito che richiede le stesse risorse.</span><span class="sxs-lookup"><span data-stu-id="2d90b-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="2d90b-112">Se l'intestazione della scadenza non è impostata correttamente sulle risorse e se non vengono utilizzate le caratteristiche di raggruppamento e minimizzazione, l'euristica di aggiornamento del browser contrassegna le risorse come obsolete dopo alcuni giorni.</span><span class="sxs-lookup"><span data-stu-id="2d90b-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="2d90b-113">Inoltre, il browser richiede una richiesta di convalida per ogni asset.</span><span class="sxs-lookup"><span data-stu-id="2d90b-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="2d90b-114">In questo caso, l'aggregazione e la minimizzazione forniscono un miglioramento delle prestazioni anche dopo la richiesta della prima pagina.</span><span class="sxs-lookup"><span data-stu-id="2d90b-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="2d90b-115">Impacchettare</span><span class="sxs-lookup"><span data-stu-id="2d90b-115">Bundling</span></span>

<span data-ttu-id="2d90b-116">La creazione di bundle consente di combinare più file in un unico file.</span><span class="sxs-lookup"><span data-stu-id="2d90b-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="2d90b-117">L'aggregazione riduce il numero di richieste del server necessarie per eseguire il rendering di un asset Web, ad esempio una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="2d90b-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="2d90b-118">È possibile creare un numero qualsiasi di singoli bundle specificamente per CSS, JavaScript, ecc. Meno file significa meno richieste HTTP dal browser al server o dal servizio che fornisce l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="2d90b-119">Ciò si traduce in un miglioramento delle prestazioni di caricamento della prima pagina.</span><span class="sxs-lookup"><span data-stu-id="2d90b-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="2d90b-120">Minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-120">Minification</span></span>

<span data-ttu-id="2d90b-121">La minimizzazione rimuove i caratteri non necessari dal codice senza alterare la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="2d90b-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="2d90b-122">Il risultato è una riduzione significativa delle dimensioni delle risorse richieste (ad esempio CSS, immagini e file JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2d90b-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="2d90b-123">Gli effetti collaterali comuni della minimizzazione includono l'accorciamento dei nomi delle variabili a un carattere e la rimozione di commenti e spazi vuoti non necessari.</span><span class="sxs-lookup"><span data-stu-id="2d90b-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="2d90b-124">Si consideri la seguente funzione JavaScript:</span><span class="sxs-lookup"><span data-stu-id="2d90b-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="2d90b-125">La minimizzazione riduce la funzione al seguente:</span><span class="sxs-lookup"><span data-stu-id="2d90b-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="2d90b-126">Oltre a rimuovere i commenti e gli spazi vuoti non necessari, i seguenti nomi di parametri e variabili sono stati rinominati come segue:</span><span class="sxs-lookup"><span data-stu-id="2d90b-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="2d90b-127">Originale</span><span class="sxs-lookup"><span data-stu-id="2d90b-127">Original</span></span> | <span data-ttu-id="2d90b-128">Ridenominazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="2d90b-129">Impatto dell'aggregazione e della minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-129">Impact of bundling and minification</span></span>

<span data-ttu-id="2d90b-130">La tabella seguente illustra le differenze tra il caricamento individuale delle attività e l'utilizzo dell'aggregazione e della minimizzazione:</span><span class="sxs-lookup"><span data-stu-id="2d90b-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="2d90b-131">Azione</span><span class="sxs-lookup"><span data-stu-id="2d90b-131">Action</span></span> | <span data-ttu-id="2d90b-132">Con B/M</span><span class="sxs-lookup"><span data-stu-id="2d90b-132">With B/M</span></span> | <span data-ttu-id="2d90b-133">Senza B/M</span><span class="sxs-lookup"><span data-stu-id="2d90b-133">Without B/M</span></span> | <span data-ttu-id="2d90b-134">Modifica</span><span class="sxs-lookup"><span data-stu-id="2d90b-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="2d90b-135">Richieste di file</span><span class="sxs-lookup"><span data-stu-id="2d90b-135">File Requests</span></span>  | <span data-ttu-id="2d90b-136">7</span><span class="sxs-lookup"><span data-stu-id="2d90b-136">7</span></span>   | <span data-ttu-id="2d90b-137">18</span><span class="sxs-lookup"><span data-stu-id="2d90b-137">18</span></span>     | <span data-ttu-id="2d90b-138">157%</span><span class="sxs-lookup"><span data-stu-id="2d90b-138">157%</span></span>
<span data-ttu-id="2d90b-139">KB trasferito</span><span class="sxs-lookup"><span data-stu-id="2d90b-139">KB Transferred</span></span> | <span data-ttu-id="2d90b-140">156</span><span class="sxs-lookup"><span data-stu-id="2d90b-140">156</span></span> | <span data-ttu-id="2d90b-141">264.68</span><span class="sxs-lookup"><span data-stu-id="2d90b-141">264.68</span></span> | <span data-ttu-id="2d90b-142">70%</span><span class="sxs-lookup"><span data-stu-id="2d90b-142">70%</span></span>
<span data-ttu-id="2d90b-143">Tempo di caricamento (ms)</span><span class="sxs-lookup"><span data-stu-id="2d90b-143">Load Time (ms)</span></span> | <span data-ttu-id="2d90b-144">885</span><span class="sxs-lookup"><span data-stu-id="2d90b-144">885</span></span> | <span data-ttu-id="2d90b-145">2360</span><span class="sxs-lookup"><span data-stu-id="2d90b-145">2360</span></span>   | <span data-ttu-id="2d90b-146">167%</span><span class="sxs-lookup"><span data-stu-id="2d90b-146">167%</span></span>

<span data-ttu-id="2d90b-147">I browser sono piuttosto dettagliati per quanto riguarda le intestazioni di richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2d90b-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="2d90b-148">La metrica dei byte totali inviati ha registrato una riduzione significativa durante l'aggregazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="2d90b-149">Il tempo di caricamento mostra un miglioramento significativo, tuttavia questo esempio è stato eseguito localmente.</span><span class="sxs-lookup"><span data-stu-id="2d90b-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="2d90b-150">Maggiori miglioramenti delle prestazioni si realizzano quando si utilizza l'aggregazione e la minimizzazione con le attività trasferite in rete.</span><span class="sxs-lookup"><span data-stu-id="2d90b-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="2d90b-151">Scegliere una strategia di raggruppamento e minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="2d90b-152">I modelli di progetto MVC e Razor Pages forniscono una soluzione predefinita per l'aggregazione e la minimizzazione costituita da un file di configurazione JSON.</span><span class="sxs-lookup"><span data-stu-id="2d90b-152">The MVC and Razor Pages project templates provide an out-of-the-box solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="2d90b-153">Strumenti di terze parti, come il corridore di attività [Grunt,](xref:client-side/using-grunt) eseguire le stesse attività con un po 'più di complessità.</span><span class="sxs-lookup"><span data-stu-id="2d90b-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="2d90b-154">Uno strumento di terze parti è ideale quando il flusso di lavoro&mdash;di sviluppo richiede l'elaborazione oltre l'aggregazione e la minimizzazione, ad esempio il linting e l'ottimizzazione delle immagini.</span><span class="sxs-lookup"><span data-stu-id="2d90b-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="2d90b-155">Utilizzando l'aggregazione e la minimizzazione in fase di progettazione, i file minimizzati vengono creati prima della distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="2d90b-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="2d90b-156">L'aggregazione e la minimizzazione prima della distribuzione offrono il vantaggio di ridurre il carico del server.</span><span class="sxs-lookup"><span data-stu-id="2d90b-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="2d90b-157">Tuttavia, è importante riconoscere che l'aggregazione e la minimizzazione in fase di progettazione aumenta la complessità di compilazione e funziona solo con i file statici.</span><span class="sxs-lookup"><span data-stu-id="2d90b-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="2d90b-158">Configurare l'aggregazione e la minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="2d90b-159">In ASP.NET Core 2.0 o versioni precedenti, i modelli di progetto MVC e Razor Pages forniscono un file di configurazione bundleconfig.json che definisce le opzioni per ogni bundle:In the Example 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span><span class="sxs-lookup"><span data-stu-id="2d90b-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2d90b-160">In ASP.NET Core 2.1 o versioni successive aggiungere un nuovo file JSON, denominato *bundleconfig.json*, alla radice del progetto MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="2d90b-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="2d90b-161">Includere il codice JSON seguente nel file come punto di partenza:Include the following JSON in that file as a starting point:</span><span class="sxs-lookup"><span data-stu-id="2d90b-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="2d90b-162">Il file *bundleconfig.json* definisce le opzioni per ogni bundle.</span><span class="sxs-lookup"><span data-stu-id="2d90b-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="2d90b-163">Nell'esempio precedente viene definita una singola configurazione bundle per i file JavaScript (*wwwroot/js/site.js*) e di foglio di stile (*wwwroot/css/site.css*).</span><span class="sxs-lookup"><span data-stu-id="2d90b-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="2d90b-164">Le opzioni di configurazione possibili sono:</span><span class="sxs-lookup"><span data-stu-id="2d90b-164">Configuration options include:</span></span>

* <span data-ttu-id="2d90b-165">`outputFileName`: il nome del file del pacchetto da restituire.</span><span class="sxs-lookup"><span data-stu-id="2d90b-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="2d90b-166">Può contenere un percorso relativo dal file *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="2d90b-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="2d90b-167">**Obbligatorio**</span><span class="sxs-lookup"><span data-stu-id="2d90b-167">**required**</span></span>
* <span data-ttu-id="2d90b-168">`inputFiles`: una serie di file da raggruppare.</span><span class="sxs-lookup"><span data-stu-id="2d90b-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="2d90b-169">Si tratta di percorsi relativi al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="2d90b-170">**facoltativo**, un valore vuoto restituisce un file di output vuoto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="2d90b-171">sono supportati i modelli [di globbing.](https://www.tldp.org/LDP/abs/html/globbingref.html)</span><span class="sxs-lookup"><span data-stu-id="2d90b-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="2d90b-172">`minify`: le opzioni di minimizzazione per il tipo di output.</span><span class="sxs-lookup"><span data-stu-id="2d90b-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="2d90b-173">**facoltativo**, *predefinito `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="2d90b-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="2d90b-174">Le opzioni di configurazione sono disponibili per ogni tipo di file di output.</span><span class="sxs-lookup"><span data-stu-id="2d90b-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="2d90b-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="2d90b-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="2d90b-176">Classificatore JavaScript</span><span class="sxs-lookup"><span data-stu-id="2d90b-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="2d90b-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="2d90b-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="2d90b-178">`includeInProject`: flag che indica se aggiungere i file generati al file di progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="2d90b-179">**facoltativo**, *default - false*</span><span class="sxs-lookup"><span data-stu-id="2d90b-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="2d90b-180">`sourceMap`: flag che indica se generare una mappa di origine per il file in bundle.</span><span class="sxs-lookup"><span data-stu-id="2d90b-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="2d90b-181">**facoltativo**, *default - false*</span><span class="sxs-lookup"><span data-stu-id="2d90b-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="2d90b-182">`sourceMapRootPath`: percorso radice per l'archiviazione del file di mappa di origine generato.</span><span class="sxs-lookup"><span data-stu-id="2d90b-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="build-time-execution-of-bundling-and-minification"></a><span data-ttu-id="2d90b-183">Esecuzione in fase di compilazione di bundling e minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-183">Build-time execution of bundling and minification</span></span>

<span data-ttu-id="2d90b-184">Il pacchetto [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet consente l'esecuzione di raggruppamento e minimizzazione in fase di compilazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-184">The [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier/) NuGet package enables the execution of bundling and minification at build time.</span></span> <span data-ttu-id="2d90b-185">Il pacchetto inserisce [destinazioni MSBuild](/visualstudio/msbuild/msbuild-targets) che vengono eseguite in fase di compilazione e pulizia.</span><span class="sxs-lookup"><span data-stu-id="2d90b-185">The package injects [MSBuild Targets](/visualstudio/msbuild/msbuild-targets) which run at build and clean time.</span></span> <span data-ttu-id="2d90b-186">Il file *bundleconfig.json* viene analizzato dal processo di compilazione per produrre i file di output in base alla configurazione definita.</span><span class="sxs-lookup"><span data-stu-id="2d90b-186">The *bundleconfig.json* file is analyzed by the build process to produce the output files based on the defined configuration.</span></span>

> [!NOTE]
> <span data-ttu-id="2d90b-187">BuildBundlerMinifier appartiene a un progetto basato sulla community su GitHub per il quale Microsoft non fornisce alcun supporto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-187">BuildBundlerMinifier belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="2d90b-188">I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="2d90b-188">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2d90b-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2d90b-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2d90b-190">Aggiungere il pacchetto *BuildBundlerMinifier* al progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-190">Add the *BuildBundlerMinifier* package to your project.</span></span>

<span data-ttu-id="2d90b-191">Compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-191">Build the project.</span></span> <span data-ttu-id="2d90b-192">Nella finestra Output viene visualizzato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="2d90b-192">The following appears in the Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Begin processing bundleconfig.json
1>  Minified wwwroot/css/site.min.css
1>  Minified wwwroot/js/site.min.js
1>Bundler: Done processing bundleconfig.json
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

<span data-ttu-id="2d90b-193">Pulire il progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-193">Clean the project.</span></span> <span data-ttu-id="2d90b-194">Nella finestra Output viene visualizzato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="2d90b-194">The following appears in the Output window:</span></span>

```console
1>------ Clean started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>
1>Bundler: Cleaning output from bundleconfig.json
1>Bundler: Done cleaning output file from bundleconfig.json
========== Clean: 1 succeeded, 0 failed, 0 skipped ==========
```

# <a name="net-core-cli"></a>[<span data-ttu-id="2d90b-195">Interfaccia della riga di comando di .NET Core</span><span class="sxs-lookup"><span data-stu-id="2d90b-195">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2d90b-196">Aggiungere il pacchetto BuildBundlerMinifier al progetto:Add the *BuildBundlerMinifier* package to your project:</span><span class="sxs-lookup"><span data-stu-id="2d90b-196">Add the *BuildBundlerMinifier* package to your project:</span></span>

```dotnetcli
dotnet add package BuildBundlerMinifier
```

<span data-ttu-id="2d90b-197">Se si usa ASP.NET Core 1.x, ripristinare il pacchetto appena aggiunto:</span><span class="sxs-lookup"><span data-stu-id="2d90b-197">If using ASP.NET Core 1.x, restore the newly added package:</span></span>

```dotnetcli
dotnet restore
```

<span data-ttu-id="2d90b-198">Compilare il progetto:</span><span class="sxs-lookup"><span data-stu-id="2d90b-198">Build the project:</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="2d90b-199">Viene visualizzato quanto segue:</span><span class="sxs-lookup"><span data-stu-id="2d90b-199">The following appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


    Bundler: Begin processing bundleconfig.json
    Bundler: Done processing bundleconfig.json
    BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
```

<span data-ttu-id="2d90b-200">Pulire il progetto:</span><span class="sxs-lookup"><span data-stu-id="2d90b-200">Clean the project:</span></span>

```dotnetcli
dotnet clean
```

<span data-ttu-id="2d90b-201">Viene visualizzato l'output seguente:</span><span class="sxs-lookup"><span data-stu-id="2d90b-201">The following output appears:</span></span>

```console
Microsoft (R) Build Engine version 15.4.8.50001 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.


  Bundler: Cleaning output from bundleconfig.json
  Bundler: Done cleaning output file from bundleconfig.json
```

---

## <a name="ad-hoc-execution-of-bundling-and-minification"></a><span data-ttu-id="2d90b-202">Esecuzione ad hoc di raggruppamento e minimizzazione</span><span class="sxs-lookup"><span data-stu-id="2d90b-202">Ad hoc execution of bundling and minification</span></span>

<span data-ttu-id="2d90b-203">È possibile eseguire le attività di raggruppamento e minimizzazione su base ad hoc, senza compilare il progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-203">It's possible to run the bundling and minification tasks on an ad hoc basis, without building the project.</span></span> <span data-ttu-id="2d90b-204">Aggiungere il pacchetto BundlerMinifier.Core NuGet al progetto:Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span><span class="sxs-lookup"><span data-stu-id="2d90b-204">Add the [BundlerMinifier.Core](https://www.nuget.org/packages/BundlerMinifier.Core/) NuGet package to your project:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=10)]

> [!NOTE]
> <span data-ttu-id="2d90b-205">BundlerMinifier.Core appartiene a un progetto basato sulla community su GitHub per il quale Microsoft non fornisce alcun supporto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-205">BundlerMinifier.Core belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="2d90b-206">I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="2d90b-206">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="2d90b-207">Questo pacchetto estende l'interfaccia della riga di comando di .NET Core per includere lo strumento *dotnet-bundle.*</span><span class="sxs-lookup"><span data-stu-id="2d90b-207">This package extends the .NET Core CLI to include the *dotnet-bundle* tool.</span></span> <span data-ttu-id="2d90b-208">Il comando seguente può essere eseguito nella finestra della console di gestione pacchetti (PMC) o in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="2d90b-208">The following command can be executed in the Package Manager Console (PMC) window or in a command shell:</span></span>

```dotnetcli
dotnet bundle
```

> [!IMPORTANT]
> <span data-ttu-id="2d90b-209">NuGet Package Manager aggiunge dipendenze al file `<PackageReference />` con estensione csproj come nodi.</span><span class="sxs-lookup"><span data-stu-id="2d90b-209">NuGet Package Manager adds dependencies to the \*.csproj file as `<PackageReference />` nodes.</span></span> <span data-ttu-id="2d90b-210">Il `dotnet bundle` comando viene registrato con l'interfaccia `<DotNetCliToolReference />` della riga di comando di .NET Core solo quando viene utilizzato un nodo.</span><span class="sxs-lookup"><span data-stu-id="2d90b-210">The `dotnet bundle` command is registered with the .NET Core CLI only when a `<DotNetCliToolReference />` node is used.</span></span> <span data-ttu-id="2d90b-211">Modificare di conseguenza il file con estensione csproj.</span><span class="sxs-lookup"><span data-stu-id="2d90b-211">Modify the \*.csproj file accordingly.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="2d90b-212">Aggiungere file al flusso di lavoro</span><span class="sxs-lookup"><span data-stu-id="2d90b-212">Add files to workflow</span></span>

<span data-ttu-id="2d90b-213">Si consideri un esempio in cui viene aggiunto un file *custom.css* aggiuntivo simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="2d90b-213">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="2d90b-214">Per minify *custom.css* e impacchettarlo con *site.css* in un file *site.min.css,* aggiungete il percorso relativo a *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="2d90b-214">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="2d90b-215">In alternativa, si potrebbe usare il seguente modello di globbing:</span><span class="sxs-lookup"><span data-stu-id="2d90b-215">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="2d90b-216">Questo modello di globbing corrisponde a tutti i file CSS ed esclude il modello di file minified.</span><span class="sxs-lookup"><span data-stu-id="2d90b-216">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="2d90b-217">Compilare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-217">Build the application.</span></span> <span data-ttu-id="2d90b-218">Apri *site.min.css* e nota che il contenuto di *custom.css* viene aggiunto alla fine del file.</span><span class="sxs-lookup"><span data-stu-id="2d90b-218">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="2d90b-219">Raggruppamento e minimizzazione basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="2d90b-219">Environment-based bundling and minification</span></span>

<span data-ttu-id="2d90b-220">Come procedura consigliata, i file in bundle e minificati dell'app devono essere usati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-220">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="2d90b-221">Durante lo sviluppo, i file originali semplificano il debug dell'app.</span><span class="sxs-lookup"><span data-stu-id="2d90b-221">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="2d90b-222">Specificare i file da includere nelle pagine utilizzando [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2d90b-222">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="2d90b-223">Environment Tag Helper esegue il rendering del contenuto solo quando viene eseguito in [ambienti](xref:fundamentals/environments)specifici.</span><span class="sxs-lookup"><span data-stu-id="2d90b-223">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="2d90b-224">Il `environment` tag seguente esegue il rendering dei `Development` file CSS non elaborati durante l'esecuzione nell'ambiente:</span><span class="sxs-lookup"><span data-stu-id="2d90b-224">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="2d90b-225">Il `environment` tag seguente esegue il rendering dei file CSS in `Development`bundle e minificati durante l'esecuzione in un ambiente diverso da .</span><span class="sxs-lookup"><span data-stu-id="2d90b-225">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="2d90b-226">Ad esempio, `Production` l'esecuzione o `Staging` l'attivazione del rendering di questi fogli di stile:</span><span class="sxs-lookup"><span data-stu-id="2d90b-226">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="2d90b-227">Usare bundleconfig.json da GulpConsume bundleconfig.json from Gulp</span><span class="sxs-lookup"><span data-stu-id="2d90b-227">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="2d90b-228">In alcuni casi il flusso di lavoro di aggregazione e minimizzazione di un'app richiede un'ulteriore elaborazione.</span><span class="sxs-lookup"><span data-stu-id="2d90b-228">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="2d90b-229">Gli esempi includono l'ottimizzazione delle immagini, il busting della cache e l'elaborazione delle risorse CDN.</span><span class="sxs-lookup"><span data-stu-id="2d90b-229">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="2d90b-230">Per soddisfare questi requisiti, è possibile convertire il flusso di lavoro di raggruppamento e minimizzazione per utilizzare Gulp.</span><span class="sxs-lookup"><span data-stu-id="2d90b-230">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="use-the-bundler--minifier-extension"></a><span data-ttu-id="2d90b-231">Usare l'estensione Bundler & Minifier</span><span class="sxs-lookup"><span data-stu-id="2d90b-231">Use the Bundler & Minifier extension</span></span>

<span data-ttu-id="2d90b-232">L'estensione & Minifier di Visual Studio Gestisce la conversione in Gulp.The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span><span class="sxs-lookup"><span data-stu-id="2d90b-232">The Visual Studio [Bundler & Minifier](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.BundlerMinifier) extension handles the conversion to Gulp.</span></span>

> [!NOTE]
> <span data-ttu-id="2d90b-233">Il Bundler & Minifier estensione appartiene a un progetto basato sulla comunità su GitHub per il quale Microsoft non fornisce alcun supporto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-233">The Bundler & Minifier extension belongs to a community-driven project on GitHub for which Microsoft provides no support.</span></span> <span data-ttu-id="2d90b-234">I problemi dovrebbero essere depositati [qui](https://github.com/madskristensen/BundlerMinifier/issues).</span><span class="sxs-lookup"><span data-stu-id="2d90b-234">Issues should be filed [here](https://github.com/madskristensen/BundlerMinifier/issues).</span></span>

<span data-ttu-id="2d90b-235">Fare clic con il pulsante destro del mouse sul file *bundleconfig.json* in Esplora soluzioni e selezionare **Bundler & Minifier** > **Converti in Gulp...**:</span><span class="sxs-lookup"><span data-stu-id="2d90b-235">Right-click the *bundleconfig.json* file in Solution Explorer and select **Bundler & Minifier** > **Convert To Gulp...**:</span></span>

![Voce di menu di scelta rapida Converti in Gulp](../client-side/bundling-and-minification/_static/convert-to-gulp.png)

<span data-ttu-id="2d90b-237">I file *gulpfile.js* e *package.json* vengono aggiunti al progetto.</span><span class="sxs-lookup"><span data-stu-id="2d90b-237">The *gulpfile.js* and *package.json* files are added to the project.</span></span> <span data-ttu-id="2d90b-238">Vengono installati i pacchetti [npm](https://www.npmjs.com/) di supporto `devDependencies` elencati nella sezione del file *package.json.*</span><span class="sxs-lookup"><span data-stu-id="2d90b-238">The supporting [npm](https://www.npmjs.com/) packages listed in the *package.json* file's `devDependencies` section are installed.</span></span>

<span data-ttu-id="2d90b-239">Eseguire il comando seguente nella finestra PMC per installare l'interfaccia della riga di comando Gulp come dipendenza globale:</span><span class="sxs-lookup"><span data-stu-id="2d90b-239">Run the following command in the PMC window to install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="2d90b-240">Il file *gulpfile.js* legge il file *bundleconfig.json* per gli input, gli output e le impostazioni.</span><span class="sxs-lookup"><span data-stu-id="2d90b-240">The *gulpfile.js* file reads the *bundleconfig.json* file for the inputs, outputs, and settings.</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-12&highlight=10)]

### <a name="convert-manually"></a><span data-ttu-id="2d90b-241">Convertire manualmente</span><span class="sxs-lookup"><span data-stu-id="2d90b-241">Convert manually</span></span>

<span data-ttu-id="2d90b-242">Se Visual Studio e/o l'estensione Bundler & Minifier non sono disponibili, eseguire la conversione manualmente.</span><span class="sxs-lookup"><span data-stu-id="2d90b-242">If Visual Studio and/or the Bundler & Minifier extension aren't available, convert manually.</span></span>

<span data-ttu-id="2d90b-243">Aggiungere un file *package.json,* con il seguente `devDependencies`, alla radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="2d90b-243">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="2d90b-244">Il `gulp-uglify` modulo non supporta ECMAScript (ES) 2015 / ES6 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="2d90b-244">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="2d90b-245">Installare [gulp-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` invece di utilizzare ES2015 / ES6 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="2d90b-245">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="2d90b-246">Installare le dipendenze eseguendo il comando seguente allo stesso livello di *package.json*:</span><span class="sxs-lookup"><span data-stu-id="2d90b-246">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="2d90b-247">Installare l'interfaccia della riga di comando Gulp come dipendenza globale:Install the Gulp CLI as a global dependency:</span><span class="sxs-lookup"><span data-stu-id="2d90b-247">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="2d90b-248">Copiare il file *gulpfile.js* riportato di seguito nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="2d90b-248">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="2d90b-249">Eseguire le attività GulpRun Gulp tasks</span><span class="sxs-lookup"><span data-stu-id="2d90b-249">Run Gulp tasks</span></span>

<span data-ttu-id="2d90b-250">Per attivare l'attività di minimizzazione Gulp prima della compilazione del progetto in Visual Studio, aggiungere la destinazione [MSBuild](/visualstudio/msbuild/msbuild-targets) seguente al file con estensione csproj:</span><span class="sxs-lookup"><span data-stu-id="2d90b-250">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="2d90b-251">In questo esempio, tutte `MyPreCompileTarget` le attività definite `Build` all'interno della destinazione vengono eseguite prima della destinazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2d90b-251">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="2d90b-252">Nell'output simile al seguente viene visualizzato nella finestra Output di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="2d90b-252">Output similar to the following appears in Visual Studio's Output window:</span></span>

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a><span data-ttu-id="2d90b-253">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2d90b-253">Additional resources</span></span>

* [<span data-ttu-id="2d90b-254">Usare Grunt</span><span class="sxs-lookup"><span data-stu-id="2d90b-254">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="2d90b-255">Usare più ambienti</span><span class="sxs-lookup"><span data-stu-id="2d90b-255">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="2d90b-256">Helper tag</span><span class="sxs-lookup"><span data-stu-id="2d90b-256">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
