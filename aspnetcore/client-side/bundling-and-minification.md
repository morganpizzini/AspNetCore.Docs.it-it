---
title: Bundle e minimifico le risorse statiche in ASP.NET Core
author: scottaddie
description: Informazioni su come ottimizzare le risorse statiche in un'applicazione Web ASP.NET Core applicando tecniche di raggruppamento e minimizzazione.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/15/2020
uid: client-side/bundling-and-minification
ms.openlocfilehash: 670ac6a96c3affd2b2ac699836f536aea7d85ff3
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488689"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="7e15e-103">Bundle e minimifico le risorse statiche in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7e15e-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="7e15e-104">Di [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="7e15e-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="7e15e-105">In questo articolo vengono illustrati i vantaggi dell'applicazione di raggruppamento e minimizzazione, incluso il modo in cui queste funzionalità possono essere utilizzate con le app Web di base di ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7e15e-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="7e15e-106">Che cos'è l'aggregazione e la minimizzazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-106">What is bundling and minification</span></span>

<span data-ttu-id="7e15e-107">L'aggregazione e la minimizzazione sono due ottimizzazioni delle prestazioni distinte che è possibile applicare in un'app Web.</span><span class="sxs-lookup"><span data-stu-id="7e15e-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="7e15e-108">Utilizzati insieme, l'aggregazione e la minimizzazione migliorano le prestazioni riducendo il numero di richieste del server e riducendo le dimensioni degli asset statici richiesti.</span><span class="sxs-lookup"><span data-stu-id="7e15e-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="7e15e-109">L'aggregazione e la minimizzazione migliorano principalmente il tempo di caricamento della prima richiesta di pagina.</span><span class="sxs-lookup"><span data-stu-id="7e15e-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="7e15e-110">Una volta richiesta una pagina Web, il browser memorizza nella cache le risorse statiche (JavaScript, CSS e immagini).</span><span class="sxs-lookup"><span data-stu-id="7e15e-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="7e15e-111">Di conseguenza, l'aggregazione e la minimizzazione non migliorano le prestazioni quando si richiede la stessa pagina, o pagine, sullo stesso sito che richiede le stesse risorse.</span><span class="sxs-lookup"><span data-stu-id="7e15e-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="7e15e-112">Se l'intestazione della scadenza non è impostata correttamente sulle risorse e se non vengono utilizzate le caratteristiche di raggruppamento e minimizzazione, l'euristica di aggiornamento del browser contrassegna le risorse come obsolete dopo alcuni giorni.</span><span class="sxs-lookup"><span data-stu-id="7e15e-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="7e15e-113">Inoltre, il browser richiede una richiesta di convalida per ogni asset.</span><span class="sxs-lookup"><span data-stu-id="7e15e-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="7e15e-114">In questo caso, l'aggregazione e la minimizzazione forniscono un miglioramento delle prestazioni anche dopo la richiesta della prima pagina.</span><span class="sxs-lookup"><span data-stu-id="7e15e-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="7e15e-115">Impacchettare</span><span class="sxs-lookup"><span data-stu-id="7e15e-115">Bundling</span></span>

<span data-ttu-id="7e15e-116">La creazione di bundle consente di combinare più file in un unico file.</span><span class="sxs-lookup"><span data-stu-id="7e15e-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="7e15e-117">L'aggregazione riduce il numero di richieste del server necessarie per eseguire il rendering di un asset Web, ad esempio una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="7e15e-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="7e15e-118">È possibile creare un numero qualsiasi di singoli bundle specificamente per CSS, JavaScript, ecc. Meno file significa meno richieste HTTP dal browser al server o dal servizio che fornisce l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="7e15e-119">Ciò si traduce in un miglioramento delle prestazioni di caricamento della prima pagina.</span><span class="sxs-lookup"><span data-stu-id="7e15e-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="7e15e-120">Minimizzazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-120">Minification</span></span>

<span data-ttu-id="7e15e-121">La minimizzazione rimuove i caratteri non necessari dal codice senza alterare la funzionalità.</span><span class="sxs-lookup"><span data-stu-id="7e15e-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="7e15e-122">Il risultato è una riduzione significativa delle dimensioni delle risorse richieste (ad esempio CSS, immagini e file JavaScript).</span><span class="sxs-lookup"><span data-stu-id="7e15e-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="7e15e-123">Gli effetti collaterali comuni della minimizzazione includono l'accorciamento dei nomi delle variabili a un carattere e la rimozione di commenti e spazi vuoti non necessari.</span><span class="sxs-lookup"><span data-stu-id="7e15e-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="7e15e-124">Si consideri la seguente funzione JavaScript:</span><span class="sxs-lookup"><span data-stu-id="7e15e-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="7e15e-125">La minimizzazione riduce la funzione al seguente:</span><span class="sxs-lookup"><span data-stu-id="7e15e-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="7e15e-126">Oltre a rimuovere i commenti e gli spazi vuoti non necessari, i seguenti nomi di parametri e variabili sono stati rinominati come segue:</span><span class="sxs-lookup"><span data-stu-id="7e15e-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="7e15e-127">Originale</span><span class="sxs-lookup"><span data-stu-id="7e15e-127">Original</span></span> | <span data-ttu-id="7e15e-128">Ridenominazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="7e15e-129">Impatto dell'aggregazione e della minimizzazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-129">Impact of bundling and minification</span></span>

<span data-ttu-id="7e15e-130">La tabella seguente illustra le differenze tra il caricamento individuale delle attività e l'utilizzo dell'aggregazione e della minimizzazione:</span><span class="sxs-lookup"><span data-stu-id="7e15e-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="7e15e-131">Azione</span><span class="sxs-lookup"><span data-stu-id="7e15e-131">Action</span></span> | <span data-ttu-id="7e15e-132">Con B/M</span><span class="sxs-lookup"><span data-stu-id="7e15e-132">With B/M</span></span> | <span data-ttu-id="7e15e-133">Senza B/M</span><span class="sxs-lookup"><span data-stu-id="7e15e-133">Without B/M</span></span> | <span data-ttu-id="7e15e-134">Modifica</span><span class="sxs-lookup"><span data-stu-id="7e15e-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="7e15e-135">Richieste di file</span><span class="sxs-lookup"><span data-stu-id="7e15e-135">File Requests</span></span>  | <span data-ttu-id="7e15e-136">7</span><span class="sxs-lookup"><span data-stu-id="7e15e-136">7</span></span>   | <span data-ttu-id="7e15e-137">18</span><span class="sxs-lookup"><span data-stu-id="7e15e-137">18</span></span>     | <span data-ttu-id="7e15e-138">157%</span><span class="sxs-lookup"><span data-stu-id="7e15e-138">157%</span></span>
<span data-ttu-id="7e15e-139">KB trasferito</span><span class="sxs-lookup"><span data-stu-id="7e15e-139">KB Transferred</span></span> | <span data-ttu-id="7e15e-140">156</span><span class="sxs-lookup"><span data-stu-id="7e15e-140">156</span></span> | <span data-ttu-id="7e15e-141">264.68</span><span class="sxs-lookup"><span data-stu-id="7e15e-141">264.68</span></span> | <span data-ttu-id="7e15e-142">70%</span><span class="sxs-lookup"><span data-stu-id="7e15e-142">70%</span></span>
<span data-ttu-id="7e15e-143">Tempo di caricamento (ms)</span><span class="sxs-lookup"><span data-stu-id="7e15e-143">Load Time (ms)</span></span> | <span data-ttu-id="7e15e-144">885</span><span class="sxs-lookup"><span data-stu-id="7e15e-144">885</span></span> | <span data-ttu-id="7e15e-145">2360</span><span class="sxs-lookup"><span data-stu-id="7e15e-145">2360</span></span>   | <span data-ttu-id="7e15e-146">167%</span><span class="sxs-lookup"><span data-stu-id="7e15e-146">167%</span></span>

<span data-ttu-id="7e15e-147">I browser sono piuttosto dettagliati per quanto riguarda le intestazioni di richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="7e15e-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="7e15e-148">La metrica dei byte totali inviati ha registrato una riduzione significativa durante l'aggregazione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="7e15e-149">Il tempo di caricamento mostra un miglioramento significativo, tuttavia questo esempio è stato eseguito localmente.</span><span class="sxs-lookup"><span data-stu-id="7e15e-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="7e15e-150">Maggiori miglioramenti delle prestazioni si realizzano quando si utilizza l'aggregazione e la minimizzazione con le attività trasferite in rete.</span><span class="sxs-lookup"><span data-stu-id="7e15e-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="7e15e-151">Scegliere una strategia di raggruppamento e minimizzazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="7e15e-152">I modelli di progetto MVC e Razor Pages forniscono una soluzione per l'aggregazione e la minimizzazione costituita da un file di configurazione JSON.</span><span class="sxs-lookup"><span data-stu-id="7e15e-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="7e15e-153">Strumenti di terze parti, come il corridore di attività [Grunt,](xref:client-side/using-grunt) eseguire le stesse attività con un po 'più di complessità.</span><span class="sxs-lookup"><span data-stu-id="7e15e-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="7e15e-154">Uno strumento di terze parti è ideale quando il flusso di lavoro&mdash;di sviluppo richiede l'elaborazione oltre l'aggregazione e la minimizzazione, ad esempio il linting e l'ottimizzazione delle immagini.</span><span class="sxs-lookup"><span data-stu-id="7e15e-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="7e15e-155">Utilizzando l'aggregazione e la minimizzazione in fase di progettazione, i file minimizzati vengono creati prima della distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e15e-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="7e15e-156">L'aggregazione e la minimizzazione prima della distribuzione offrono il vantaggio di ridurre il carico del server.</span><span class="sxs-lookup"><span data-stu-id="7e15e-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="7e15e-157">Tuttavia, è importante riconoscere che l'aggregazione e la minimizzazione in fase di progettazione aumenta la complessità di compilazione e funziona solo con i file statici.</span><span class="sxs-lookup"><span data-stu-id="7e15e-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7e15e-158">Configurare l'aggregazione e la minimizzazione</span><span class="sxs-lookup"><span data-stu-id="7e15e-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="7e15e-159">In ASP.NET Core 2.0 o versioni precedenti, i modelli di progetto MVC e Razor Pages forniscono un file di configurazione bundleconfig.json che definisce le opzioni per ogni bundle:In the Example 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span><span class="sxs-lookup"><span data-stu-id="7e15e-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="7e15e-160">In ASP.NET Core 2.1 o versioni successive aggiungere un nuovo file JSON, denominato *bundleconfig.json*, alla radice del progetto MVC o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="7e15e-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="7e15e-161">Includere il codice JSON seguente nel file come punto di partenza:Include the following JSON in that file as a starting point:</span><span class="sxs-lookup"><span data-stu-id="7e15e-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="7e15e-162">Il file *bundleconfig.json* definisce le opzioni per ogni bundle.</span><span class="sxs-lookup"><span data-stu-id="7e15e-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="7e15e-163">Nell'esempio precedente viene definita una singola configurazione bundle per i file JavaScript (*wwwroot/js/site.js*) e di foglio di stile (*wwwroot/css/site.css*).</span><span class="sxs-lookup"><span data-stu-id="7e15e-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="7e15e-164">Le opzioni di configurazione possibili sono:</span><span class="sxs-lookup"><span data-stu-id="7e15e-164">Configuration options include:</span></span>

* <span data-ttu-id="7e15e-165">`outputFileName`: il nome del file del pacchetto da restituire.</span><span class="sxs-lookup"><span data-stu-id="7e15e-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="7e15e-166">Può contenere un percorso relativo dal file *bundleconfig.json.*</span><span class="sxs-lookup"><span data-stu-id="7e15e-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="7e15e-167">**Obbligatorio**</span><span class="sxs-lookup"><span data-stu-id="7e15e-167">**required**</span></span>
* <span data-ttu-id="7e15e-168">`inputFiles`: una serie di file da raggruppare.</span><span class="sxs-lookup"><span data-stu-id="7e15e-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="7e15e-169">Si tratta di percorsi relativi al file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="7e15e-170">**facoltativo**, un valore vuoto restituisce un file di output vuoto.</span><span class="sxs-lookup"><span data-stu-id="7e15e-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="7e15e-171">sono supportati i modelli [di globbing.](https://www.tldp.org/LDP/abs/html/globbingref.html)</span><span class="sxs-lookup"><span data-stu-id="7e15e-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="7e15e-172">`minify`: le opzioni di minimizzazione per il tipo di output.</span><span class="sxs-lookup"><span data-stu-id="7e15e-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="7e15e-173">**facoltativo**, *predefinito `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="7e15e-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="7e15e-174">Le opzioni di configurazione sono disponibili per ogni tipo di file di output.</span><span class="sxs-lookup"><span data-stu-id="7e15e-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="7e15e-175">CSS Minifier</span><span class="sxs-lookup"><span data-stu-id="7e15e-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="7e15e-176">Classificatore JavaScript</span><span class="sxs-lookup"><span data-stu-id="7e15e-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="7e15e-177">HTML Minifier</span><span class="sxs-lookup"><span data-stu-id="7e15e-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="7e15e-178">`includeInProject`: flag che indica se aggiungere i file generati al file di progetto.</span><span class="sxs-lookup"><span data-stu-id="7e15e-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="7e15e-179">**facoltativo**, *default - false*</span><span class="sxs-lookup"><span data-stu-id="7e15e-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="7e15e-180">`sourceMap`: flag che indica se generare una mappa di origine per il file in bundle.</span><span class="sxs-lookup"><span data-stu-id="7e15e-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="7e15e-181">**facoltativo**, *default - false*</span><span class="sxs-lookup"><span data-stu-id="7e15e-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="7e15e-182">`sourceMapRootPath`: percorso radice per l'archiviazione del file di mappa di origine generato.</span><span class="sxs-lookup"><span data-stu-id="7e15e-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="7e15e-183">Aggiungere file al flusso di lavoro</span><span class="sxs-lookup"><span data-stu-id="7e15e-183">Add files to workflow</span></span>

<span data-ttu-id="7e15e-184">Si consideri un esempio in cui viene aggiunto un file *custom.css* aggiuntivo simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="7e15e-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="7e15e-185">Per minify *custom.css* e impacchettarlo con *site.css* in un file *site.min.css,* aggiungete il percorso relativo a *bundleconfig.json*:</span><span class="sxs-lookup"><span data-stu-id="7e15e-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="7e15e-186">In alternativa, si potrebbe usare il seguente modello di globbing:</span><span class="sxs-lookup"><span data-stu-id="7e15e-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="7e15e-187">Questo modello di globbing corrisponde a tutti i file CSS ed esclude il modello di file minified.</span><span class="sxs-lookup"><span data-stu-id="7e15e-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="7e15e-188">Compilare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-188">Build the application.</span></span> <span data-ttu-id="7e15e-189">Apri *site.min.css* e nota che il contenuto di *custom.css* viene aggiunto alla fine del file.</span><span class="sxs-lookup"><span data-stu-id="7e15e-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="7e15e-190">Raggruppamento e minimizzazione basati sull'ambiente</span><span class="sxs-lookup"><span data-stu-id="7e15e-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="7e15e-191">Come procedura consigliata, i file in bundle e minificati dell'app devono essere usati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="7e15e-192">Durante lo sviluppo, i file originali semplificano il debug dell'app.</span><span class="sxs-lookup"><span data-stu-id="7e15e-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="7e15e-193">Specificare i file da includere nelle pagine utilizzando [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="7e15e-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="7e15e-194">Environment Tag Helper esegue il rendering del contenuto solo quando viene eseguito in [ambienti](xref:fundamentals/environments)specifici.</span><span class="sxs-lookup"><span data-stu-id="7e15e-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="7e15e-195">Il `environment` tag seguente esegue il rendering dei `Development` file CSS non elaborati durante l'esecuzione nell'ambiente:</span><span class="sxs-lookup"><span data-stu-id="7e15e-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="7e15e-196">Il `environment` tag seguente esegue il rendering dei file CSS in `Development`bundle e minificati durante l'esecuzione in un ambiente diverso da .</span><span class="sxs-lookup"><span data-stu-id="7e15e-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="7e15e-197">Ad esempio, `Production` l'esecuzione o `Staging` l'attivazione del rendering di questi fogli di stile:</span><span class="sxs-lookup"><span data-stu-id="7e15e-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="7e15e-198">Usare bundleconfig.json da GulpConsume bundleconfig.json from Gulp</span><span class="sxs-lookup"><span data-stu-id="7e15e-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="7e15e-199">In alcuni casi il flusso di lavoro di aggregazione e minimizzazione di un'app richiede un'ulteriore elaborazione.</span><span class="sxs-lookup"><span data-stu-id="7e15e-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="7e15e-200">Gli esempi includono l'ottimizzazione delle immagini, il busting della cache e l'elaborazione delle risorse CDN.</span><span class="sxs-lookup"><span data-stu-id="7e15e-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="7e15e-201">Per soddisfare questi requisiti, è possibile convertire il flusso di lavoro di raggruppamento e minimizzazione per utilizzare Gulp.</span><span class="sxs-lookup"><span data-stu-id="7e15e-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="7e15e-202">Convertire manualmente il flusso di lavoro di raggruppamento e minimizzazione per utilizzare Gulp</span><span class="sxs-lookup"><span data-stu-id="7e15e-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="7e15e-203">Aggiungere un file *package.json,* con il seguente `devDependencies`, alla radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="7e15e-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="7e15e-204">Il `gulp-uglify` modulo non supporta ECMAScript (ES) 2015 / ES6 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7e15e-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="7e15e-205">Installare [gulp-terser](https://www.npmjs.com/package/gulp-terser) `gulp-uglify` invece di utilizzare ES2015 / ES6 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="7e15e-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="7e15e-206">Installare le dipendenze eseguendo il comando seguente allo stesso livello di *package.json*:</span><span class="sxs-lookup"><span data-stu-id="7e15e-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```console
npm i
```

<span data-ttu-id="7e15e-207">Installare l'interfaccia della riga di comando Gulp come dipendenza globale:Install the Gulp CLI as a global dependency:</span><span class="sxs-lookup"><span data-stu-id="7e15e-207">Install the Gulp CLI as a global dependency:</span></span>

```console
npm i -g gulp-cli
```

<span data-ttu-id="7e15e-208">Copiare il file *gulpfile.js* riportato di seguito nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="7e15e-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="7e15e-209">Eseguire le attività GulpRun Gulp tasks</span><span class="sxs-lookup"><span data-stu-id="7e15e-209">Run Gulp tasks</span></span>

<span data-ttu-id="7e15e-210">Per attivare l'attività di minimizzazione Gulp prima della compilazione del progetto in Visual Studio, aggiungere la destinazione [MSBuild](/visualstudio/msbuild/msbuild-targets) seguente al file con estensione csproj:</span><span class="sxs-lookup"><span data-stu-id="7e15e-210">To trigger the Gulp minification task before the project builds in Visual Studio, add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the \*.csproj file:</span></span>

[!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="7e15e-211">In questo esempio, tutte `MyPreCompileTarget` le attività definite `Build` all'interno della destinazione vengono eseguite prima della destinazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="7e15e-211">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="7e15e-212">Nell'output simile al seguente viene visualizzato nella finestra Output di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7e15e-212">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="7e15e-213">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="7e15e-213">Additional resources</span></span>

* [<span data-ttu-id="7e15e-214">Usare Grunt</span><span class="sxs-lookup"><span data-stu-id="7e15e-214">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="7e15e-215">Usare più ambienti</span><span class="sxs-lookup"><span data-stu-id="7e15e-215">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="7e15e-216">Helper tag</span><span class="sxs-lookup"><span data-stu-id="7e15e-216">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
