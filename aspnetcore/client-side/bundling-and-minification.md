---
title: Aggregare e minimizzare asset statici in ASP.NET Core
author: scottaddie
description: Informazioni su come ottimizzare le risorse statiche in un'applicazione Web ASP.NET Core applicando tecniche di aggregazione e minification.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/bundling-and-minification
ms.openlocfilehash: 84123464e8f01f8a3caa65035b3174cc04aea7cf
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625856"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="afa15-103">Aggregare e minimizzare asset statici in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="afa15-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="afa15-104">Di [Scott Addie](https://twitter.com/Scott_Addie) e [David Pine](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="afa15-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="afa15-105">Questo articolo illustra i vantaggi dell'applicazione di bundle e minification, incluso il modo in cui queste funzionalità possono essere usate con ASP.NET Core app Web.</span><span class="sxs-lookup"><span data-stu-id="afa15-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="afa15-106">Che cos'è la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="afa15-106">What is bundling and minification</span></span>

<span data-ttu-id="afa15-107">La creazione di bundle e minification sono due ottimizzazioni delle prestazioni distinte che è possibile applicare in un'app Web.</span><span class="sxs-lookup"><span data-stu-id="afa15-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="afa15-108">Usati insieme, bundleing e minification migliorano le prestazioni riducendo il numero di richieste server e riducendo le dimensioni degli asset statici richiesti.</span><span class="sxs-lookup"><span data-stu-id="afa15-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="afa15-109">La creazione di bundle e minification migliora principalmente il tempo di caricamento della prima richiesta di pagina.</span><span class="sxs-lookup"><span data-stu-id="afa15-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="afa15-110">Una volta richiesta una pagina Web, il browser memorizza nella cache gli asset statici (JavaScript, CSS e immagini).</span><span class="sxs-lookup"><span data-stu-id="afa15-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="afa15-111">Di conseguenza, la creazione di bundle e minification non migliora le prestazioni quando si richiede la stessa pagina o pagine nello stesso sito che richiede le stesse risorse.</span><span class="sxs-lookup"><span data-stu-id="afa15-111">Consequently, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="afa15-112">Se l'intestazione Expires non è impostata correttamente negli asset e se non si usa la funzionalità di aggregazione e minification, l'euristica di aggiornamento del browser contrassegna gli asset obsoleti dopo alcuni giorni.</span><span class="sxs-lookup"><span data-stu-id="afa15-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="afa15-113">Inoltre, il browser richiede una richiesta di convalida per ogni asset.</span><span class="sxs-lookup"><span data-stu-id="afa15-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="afa15-114">In questo caso, la creazione di bundle e minification fornisce un miglioramento delle prestazioni anche dopo la prima richiesta di pagina.</span><span class="sxs-lookup"><span data-stu-id="afa15-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="afa15-115">Bundle</span><span class="sxs-lookup"><span data-stu-id="afa15-115">Bundling</span></span>

<span data-ttu-id="afa15-116">La creazione di bundle consente di combinare più file in un unico file.</span><span class="sxs-lookup"><span data-stu-id="afa15-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="afa15-117">La creazione di bundle riduce il numero di richieste del server necessarie per il rendering di un asset Web, ad esempio una pagina Web.</span><span class="sxs-lookup"><span data-stu-id="afa15-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="afa15-118">È possibile creare un numero qualsiasi di bundle individuali in modo specifico per CSS, JavaScript e così via. Un numero inferiore di file indica un minor numero di richieste HTTP dal browser al server o dal servizio che fornisce l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="afa15-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files means fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="afa15-119">Ciò comporta un miglioramento delle prime prestazioni di caricamento della pagina.</span><span class="sxs-lookup"><span data-stu-id="afa15-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="afa15-120">Minimizzazione</span><span class="sxs-lookup"><span data-stu-id="afa15-120">Minification</span></span>

<span data-ttu-id="afa15-121">Minification rimuove i caratteri non necessari dal codice senza alterare le funzionalità.</span><span class="sxs-lookup"><span data-stu-id="afa15-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="afa15-122">Il risultato è una riduzione significativa delle dimensioni negli asset richiesti, ad esempio CSS, immagini e file JavaScript.</span><span class="sxs-lookup"><span data-stu-id="afa15-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="afa15-123">Gli effetti collaterali comuni di minification includono l'abbreviazione di nomi di variabili a un carattere e la rimozione di commenti e spazi vuoti superflui.</span><span class="sxs-lookup"><span data-stu-id="afa15-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="afa15-124">Si consideri la funzione JavaScript seguente:</span><span class="sxs-lookup"><span data-stu-id="afa15-124">Consider the following JavaScript function:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

<span data-ttu-id="afa15-125">Minification riduce la funzione a quanto segue:</span><span class="sxs-lookup"><span data-stu-id="afa15-125">Minification reduces the function to the following:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

<span data-ttu-id="afa15-126">Oltre a rimuovere i commenti e gli spazi vuoti superflui, i nomi dei parametri e delle variabili seguenti sono stati rinominati come segue:</span><span class="sxs-lookup"><span data-stu-id="afa15-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="afa15-127">Originale</span><span class="sxs-lookup"><span data-stu-id="afa15-127">Original</span></span> | <span data-ttu-id="afa15-128">Ridenominazione</span><span class="sxs-lookup"><span data-stu-id="afa15-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="afa15-129">Effetti della creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="afa15-129">Impact of bundling and minification</span></span>

<span data-ttu-id="afa15-130">Nella tabella seguente vengono descritte le differenze tra il caricamento individuale degli asset e l'utilizzo di bundle e minification:</span><span class="sxs-lookup"><span data-stu-id="afa15-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="afa15-131">Azione</span><span class="sxs-lookup"><span data-stu-id="afa15-131">Action</span></span> | <span data-ttu-id="afa15-132">Con B/M</span><span class="sxs-lookup"><span data-stu-id="afa15-132">With B/M</span></span> | <span data-ttu-id="afa15-133">Senza B/M</span><span class="sxs-lookup"><span data-stu-id="afa15-133">Without B/M</span></span> | <span data-ttu-id="afa15-134">Modifica</span><span class="sxs-lookup"><span data-stu-id="afa15-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="afa15-135">Richieste di file</span><span class="sxs-lookup"><span data-stu-id="afa15-135">File Requests</span></span>  | <span data-ttu-id="afa15-136">7</span><span class="sxs-lookup"><span data-stu-id="afa15-136">7</span></span>   | <span data-ttu-id="afa15-137">18</span><span class="sxs-lookup"><span data-stu-id="afa15-137">18</span></span>     | <span data-ttu-id="afa15-138">157%</span><span class="sxs-lookup"><span data-stu-id="afa15-138">157%</span></span>
<span data-ttu-id="afa15-139">KB trasferiti</span><span class="sxs-lookup"><span data-stu-id="afa15-139">KB Transferred</span></span> | <span data-ttu-id="afa15-140">156</span><span class="sxs-lookup"><span data-stu-id="afa15-140">156</span></span> | <span data-ttu-id="afa15-141">264,68</span><span class="sxs-lookup"><span data-stu-id="afa15-141">264.68</span></span> | <span data-ttu-id="afa15-142">70%</span><span class="sxs-lookup"><span data-stu-id="afa15-142">70%</span></span>
<span data-ttu-id="afa15-143">Tempo di caricamento (MS)</span><span class="sxs-lookup"><span data-stu-id="afa15-143">Load Time (ms)</span></span> | <span data-ttu-id="afa15-144">885</span><span class="sxs-lookup"><span data-stu-id="afa15-144">885</span></span> | <span data-ttu-id="afa15-145">2360</span><span class="sxs-lookup"><span data-stu-id="afa15-145">2360</span></span>   | <span data-ttu-id="afa15-146">167%</span><span class="sxs-lookup"><span data-stu-id="afa15-146">167%</span></span>

<span data-ttu-id="afa15-147">I browser sono piuttosto dettagliati rispetto alle intestazioni delle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="afa15-147">Browsers are fairly verbose with regard to HTTP request headers.</span></span> <span data-ttu-id="afa15-148">La metrica totale dei byte inviati ha rilevato una riduzione significativa durante la creazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="afa15-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="afa15-149">Il tempo di caricamento Mostra un miglioramento significativo, tuttavia questo esempio è stato eseguito localmente.</span><span class="sxs-lookup"><span data-stu-id="afa15-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="afa15-150">Quando si usa la creazione di bundle e minification con asset trasferiti in una rete, si ottengono maggiori vantaggi a livello di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="afa15-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="afa15-151">Scegliere una strategia di raggruppamento e minification</span><span class="sxs-lookup"><span data-stu-id="afa15-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="afa15-152">I modelli di Razor progetto MVC e Pages forniscono una soluzione per la creazione di bundle e minification costituiti da un file di configurazione JSON.</span><span class="sxs-lookup"><span data-stu-id="afa15-152">The MVC and Razor Pages project templates provide a solution for bundling and minification consisting of a JSON configuration file.</span></span> <span data-ttu-id="afa15-153">Gli strumenti di terze parti, ad [esempio l'attività](xref:client-side/using-grunt) Runner, eseguono le stesse attività con una maggiore complessità.</span><span class="sxs-lookup"><span data-stu-id="afa15-153">Third-party tools, such as the [Grunt](xref:client-side/using-grunt) task runner, accomplish the same tasks with a bit more complexity.</span></span> <span data-ttu-id="afa15-154">Uno strumento di terze parti è un'ottima soluzione quando il flusso di lavoro di sviluppo richiede l'elaborazione oltre la creazione di bundle e minification &mdash; , ad esempio l'ottimizzazione delle immagini e dei pelucchi.</span><span class="sxs-lookup"><span data-stu-id="afa15-154">A third-party tool is a great fit when your development workflow requires processing beyond bundling and minification&mdash;such as linting and image optimization.</span></span> <span data-ttu-id="afa15-155">Con la creazione di bundle e minification in fase di progettazione, i file minimizzati vengono creati prima della distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="afa15-155">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="afa15-156">La creazione di bundle e minimizzazione prima della distribuzione offre il vantaggio di ridurre il carico del server.</span><span class="sxs-lookup"><span data-stu-id="afa15-156">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="afa15-157">Tuttavia, è importante riconoscere che la creazione di bundle in fase di progettazione e minification aumenta la complessità della compilazione e funziona solo con i file statici.</span><span class="sxs-lookup"><span data-stu-id="afa15-157">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="afa15-158">Configurare la creazione di bundle e minification</span><span class="sxs-lookup"><span data-stu-id="afa15-158">Configure bundling and minification</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="afa15-159">In ASP.NET Core 2,0 o versioni precedenti, i modelli di progetto MVC e Razor pages forniscono una *bundleconfig.jsnel* file di configurazione che definisce le opzioni per ogni bundle:</span><span class="sxs-lookup"><span data-stu-id="afa15-159">In ASP.NET Core 2.0 or earlier, the MVC and Razor Pages project templates provide a *bundleconfig.json* configuration file that defines the options for each bundle:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="afa15-160">In ASP.NET Core 2,1 o versioni successive aggiungere un nuovo file JSON, denominato *bundleconfig.json*, alla radice del progetto MVC o Razor pages.</span><span class="sxs-lookup"><span data-stu-id="afa15-160">In ASP.NET Core 2.1 or later, add a new JSON file, named *bundleconfig.json*, to the MVC or Razor Pages project root.</span></span> <span data-ttu-id="afa15-161">Includere nel file il codice JSON seguente come punto di partenza:</span><span class="sxs-lookup"><span data-stu-id="afa15-161">Include the following JSON in that file as a starting point:</span></span>

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

<span data-ttu-id="afa15-162">Il *bundleconfig.jsnel* file definisce le opzioni per ogni bundle.</span><span class="sxs-lookup"><span data-stu-id="afa15-162">The *bundleconfig.json* file defines the options for each bundle.</span></span> <span data-ttu-id="afa15-163">Nell'esempio precedente viene definita una singola configurazione di bundle per i file JavaScript (*wwwroot/JS/site.js*) e StyleSheet (*wwwroot/CSS/site. CSS*) personalizzati.</span><span class="sxs-lookup"><span data-stu-id="afa15-163">In the preceding example, a single bundle configuration is defined for the custom JavaScript (*wwwroot/js/site.js*) and stylesheet (*wwwroot/css/site.css*) files.</span></span>

<span data-ttu-id="afa15-164">Le opzioni di configurazione possibili sono:</span><span class="sxs-lookup"><span data-stu-id="afa15-164">Configuration options include:</span></span>

* <span data-ttu-id="afa15-165">`outputFileName`: Nome del file di bundle da restituire.</span><span class="sxs-lookup"><span data-stu-id="afa15-165">`outputFileName`: The name of the bundle file to output.</span></span> <span data-ttu-id="afa15-166">Può contenere un percorso relativo dal *bundleconfig.jssul* file.</span><span class="sxs-lookup"><span data-stu-id="afa15-166">Can contain a relative path from the *bundleconfig.json* file.</span></span> <span data-ttu-id="afa15-167">**Obbligatorio**</span><span class="sxs-lookup"><span data-stu-id="afa15-167">**required**</span></span>
* <span data-ttu-id="afa15-168">`inputFiles`: Matrice di file da raggruppare.</span><span class="sxs-lookup"><span data-stu-id="afa15-168">`inputFiles`: An array of files to bundle together.</span></span> <span data-ttu-id="afa15-169">Si tratta di percorsi relativi del file di configurazione.</span><span class="sxs-lookup"><span data-stu-id="afa15-169">These are relative paths to the configuration file.</span></span> <span data-ttu-id="afa15-170">**facoltativo**, \* un valore vuoto restituisce un file di output vuoto.</span><span class="sxs-lookup"><span data-stu-id="afa15-170">**optional**, \*an empty value results in an empty output file.</span></span> <span data-ttu-id="afa15-171">sono supportati i modelli [glob](https://www.tldp.org/LDP/abs/html/globbingref.html) .</span><span class="sxs-lookup"><span data-stu-id="afa15-171">[globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) patterns are supported.</span></span>
* <span data-ttu-id="afa15-172">`minify`: Opzioni minification per il tipo di output.</span><span class="sxs-lookup"><span data-stu-id="afa15-172">`minify`: The minification options for the output type.</span></span> <span data-ttu-id="afa15-173">**facoltativo**, *valore predefinito `minify: { enabled: true }` -*</span><span class="sxs-lookup"><span data-stu-id="afa15-173">**optional**, *default - `minify: { enabled: true }`*</span></span>
  * <span data-ttu-id="afa15-174">Le opzioni di configurazione sono disponibili per ogni tipo di file di output.</span><span class="sxs-lookup"><span data-stu-id="afa15-174">Configuration options are available per output file type.</span></span>
    * [<span data-ttu-id="afa15-175">Minifier CSS</span><span class="sxs-lookup"><span data-stu-id="afa15-175">CSS Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [<span data-ttu-id="afa15-176">Minifier JavaScript</span><span class="sxs-lookup"><span data-stu-id="afa15-176">JavaScript Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [<span data-ttu-id="afa15-177">Minifier HTML</span><span class="sxs-lookup"><span data-stu-id="afa15-177">HTML Minifier</span></span>](https://github.com/madskristensen/BundlerMinifier/wiki)
* <span data-ttu-id="afa15-178">`includeInProject`: Flag che indica se aggiungere i file generati al file di progetto.</span><span class="sxs-lookup"><span data-stu-id="afa15-178">`includeInProject`: Flag indicating whether to add generated files to project file.</span></span> <span data-ttu-id="afa15-179">**facoltativo**, *valore predefinito-false*</span><span class="sxs-lookup"><span data-stu-id="afa15-179">**optional**, *default - false*</span></span>
* <span data-ttu-id="afa15-180">`sourceMap`: Flag che indica se generare una mappa di origine per il file in bundle.</span><span class="sxs-lookup"><span data-stu-id="afa15-180">`sourceMap`: Flag indicating whether to generate a source map for the bundled file.</span></span> <span data-ttu-id="afa15-181">**facoltativo**, *valore predefinito-false*</span><span class="sxs-lookup"><span data-stu-id="afa15-181">**optional**, *default - false*</span></span>
* <span data-ttu-id="afa15-182">`sourceMapRootPath`: Percorso radice per l'archiviazione del file di mapping di origine generato.</span><span class="sxs-lookup"><span data-stu-id="afa15-182">`sourceMapRootPath`: The root path for storing the generated source map file.</span></span>

## <a name="add-files-to-workflow"></a><span data-ttu-id="afa15-183">Aggiunta di file al flusso di lavoro</span><span class="sxs-lookup"><span data-stu-id="afa15-183">Add files to workflow</span></span>

<span data-ttu-id="afa15-184">Si consideri un esempio in cui viene aggiunto un file *CSS personalizzato* aggiuntivo simile al seguente:</span><span class="sxs-lookup"><span data-stu-id="afa15-184">Consider an example in which an additional *custom.css* file is added resembling the following:</span></span>

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

<span data-ttu-id="afa15-185">Per minimizzare *Custom. CSS* e aggregarlo con *site. CSS* in un file *site. min. CSS* , aggiungere il percorso relativo *bundleconfig.jsin*:</span><span class="sxs-lookup"><span data-stu-id="afa15-185">To minify *custom.css* and bundle it with *site.css* into a *site.min.css* file, add the relative path to *bundleconfig.json*:</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> <span data-ttu-id="afa15-186">In alternativa, è possibile usare il modello glob seguente:</span><span class="sxs-lookup"><span data-stu-id="afa15-186">Alternatively, the following globbing pattern could be used:</span></span>
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> <span data-ttu-id="afa15-187">Questo modello glob corrisponde a tutti i file CSS ed esclude il modello di file minimizzati.</span><span class="sxs-lookup"><span data-stu-id="afa15-187">This globbing pattern matches all CSS files and excludes the minified file pattern.</span></span>

<span data-ttu-id="afa15-188">Compilare l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="afa15-188">Build the application.</span></span> <span data-ttu-id="afa15-189">Aprire *site. min. CSS* e notare che il contenuto di *Custom. CSS* viene aggiunto alla fine del file.</span><span class="sxs-lookup"><span data-stu-id="afa15-189">Open *site.min.css* and notice the content of *custom.css* is appended to the end of the file.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="afa15-190">Creazione di bundle e minification basati su ambiente</span><span class="sxs-lookup"><span data-stu-id="afa15-190">Environment-based bundling and minification</span></span>

<span data-ttu-id="afa15-191">Come procedura consigliata, i file in bundle e minimizzati dell'app devono essere usati in un ambiente di produzione.</span><span class="sxs-lookup"><span data-stu-id="afa15-191">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="afa15-192">Durante lo sviluppo, i file originali rendono più semplice il debug dell'app.</span><span class="sxs-lookup"><span data-stu-id="afa15-192">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="afa15-193">Specificare i file da includere nelle pagine usando l' [Helper tag di ambiente](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) nelle visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="afa15-193">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="afa15-194">L'helper tag di ambiente esegue il rendering del relativo contenuto solo quando è in esecuzione in [ambienti](xref:fundamentals/environments)specifici.</span><span class="sxs-lookup"><span data-stu-id="afa15-194">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="afa15-195">Il `environment` tag seguente esegue il rendering dei file CSS non elaborati durante l'esecuzione nell' `Development` ambiente:</span><span class="sxs-lookup"><span data-stu-id="afa15-195">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

<span data-ttu-id="afa15-196">Il `environment` tag seguente esegue il rendering dei file CSS in bundle e minimizzati quando è in esecuzione in un ambiente diverso da `Development` .</span><span class="sxs-lookup"><span data-stu-id="afa15-196">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="afa15-197">Ad esempio, in esecuzione in `Production` o viene `Staging` attivato il rendering di questi fogli di stile:</span><span class="sxs-lookup"><span data-stu-id="afa15-197">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a><span data-ttu-id="afa15-198">Utilizzare bundleconfig.jsda Gulp</span><span class="sxs-lookup"><span data-stu-id="afa15-198">Consume bundleconfig.json from Gulp</span></span>

<span data-ttu-id="afa15-199">Ci sono casi in cui il flusso di lavoro di aggregazione e minification di un'app richiede un'elaborazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="afa15-199">There are cases in which an app's bundling and minification workflow requires additional processing.</span></span> <span data-ttu-id="afa15-200">Gli esempi includono l'ottimizzazione delle immagini, la memorizzazione nella cache e l'elaborazione delle risorse della rete CDN.</span><span class="sxs-lookup"><span data-stu-id="afa15-200">Examples include image optimization, cache busting, and CDN asset processing.</span></span> <span data-ttu-id="afa15-201">Per soddisfare questi requisiti, è possibile convertire il flusso di lavoro di aggregazione e minification per usare Gulp.</span><span class="sxs-lookup"><span data-stu-id="afa15-201">To satisfy these requirements, you can convert the bundling and minification workflow to use Gulp.</span></span>

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a><span data-ttu-id="afa15-202">Convertire manualmente il flusso di lavoro di aggregazione e minification per usare Gulp</span><span class="sxs-lookup"><span data-stu-id="afa15-202">Manually convert the bundling and minification workflow to use Gulp</span></span>

<span data-ttu-id="afa15-203">Aggiungere un *package.jsnel* file, con il codice seguente `devDependencies` , alla radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="afa15-203">Add a *package.json* file, with the following `devDependencies`, to the project root:</span></span>

> [!WARNING]
> <span data-ttu-id="afa15-204">Il `gulp-uglify` modulo non supporta ECMAScript (es) 2015/ES6 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="afa15-204">The `gulp-uglify` module doesn't support ECMAScript (ES) 2015 / ES6 and later.</span></span> <span data-ttu-id="afa15-205">Installare [Gulp-Terser](https://www.npmjs.com/package/gulp-terser) invece di `gulp-uglify` per usare ES2015/ES6 o versione successiva.</span><span class="sxs-lookup"><span data-stu-id="afa15-205">Install [gulp-terser](https://www.npmjs.com/package/gulp-terser) instead of `gulp-uglify` to use ES2015 / ES6 or later.</span></span>

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

<span data-ttu-id="afa15-206">Installare le dipendenze eseguendo il comando seguente allo stesso livello di *package.jsin*:</span><span class="sxs-lookup"><span data-stu-id="afa15-206">Install the dependencies by running the following command at the same level as *package.json*:</span></span>

```bash
npm i
```

<span data-ttu-id="afa15-207">Installare l'interfaccia della riga di comando di Gulp come dipendenza globale:</span><span class="sxs-lookup"><span data-stu-id="afa15-207">Install the Gulp CLI as a global dependency:</span></span>

```bash
npm i -g gulp-cli
```

<span data-ttu-id="afa15-208">Copiare il file di *gulpfile.js* seguente nella radice del progetto:</span><span class="sxs-lookup"><span data-stu-id="afa15-208">Copy the *gulpfile.js* file below to the project root:</span></span>

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a><span data-ttu-id="afa15-209">Eseguire attività Gulp</span><span class="sxs-lookup"><span data-stu-id="afa15-209">Run Gulp tasks</span></span>

<span data-ttu-id="afa15-210">Per attivare l'attività minification di Gulp prima della compilazione del progetto in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="afa15-210">To trigger the Gulp minification task before the project builds in Visual Studio:</span></span>

1. <span data-ttu-id="afa15-211">Installare il pacchetto NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .</span><span class="sxs-lookup"><span data-stu-id="afa15-211">Install the [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet package.</span></span>
1. <span data-ttu-id="afa15-212">Aggiungere la [destinazione MSBuild](/visualstudio/msbuild/msbuild-targets) seguente al file di progetto:</span><span class="sxs-lookup"><span data-stu-id="afa15-212">Add the following [MSBuild Target](/visualstudio/msbuild/msbuild-targets) to the project file:</span></span>

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

<span data-ttu-id="afa15-213">In questo esempio, tutte le attività definite nella `MyPreCompileTarget` destinazione vengono eseguite prima della `Build` destinazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="afa15-213">In this example, any tasks defined within the `MyPreCompileTarget` target run before the predefined `Build` target.</span></span> <span data-ttu-id="afa15-214">Viene visualizzato un output simile al seguente nella finestra di output di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="afa15-214">Output similar to the following appears in Visual Studio's Output window:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="afa15-215">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="afa15-215">Additional resources</span></span>

* [<span data-ttu-id="afa15-216">Usare Grunt</span><span class="sxs-lookup"><span data-stu-id="afa15-216">Use Grunt</span></span>](xref:client-side/using-grunt)
* [<span data-ttu-id="afa15-217">Usare più ambienti</span><span class="sxs-lookup"><span data-stu-id="afa15-217">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="afa15-218">Helper per i tag</span><span class="sxs-lookup"><span data-stu-id="afa15-218">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
