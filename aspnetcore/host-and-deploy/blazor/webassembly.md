---
title: Ospitare e distribuire ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 2472fd499128a8807b76a3cc031d466140e180f5
ms.sourcegitcommit: 23243f6d6a3100303802e4310b0634860cc0b268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619369"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="98916-103">Ospitare e distribuire un webassembly ASP.NET Core Blazer</span><span class="sxs-lookup"><span data-stu-id="98916-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="98916-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="98916-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="98916-105">Con il [modello di hosting Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="98916-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="98916-106">L'app blazer, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="98916-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="98916-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="98916-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="98916-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="98916-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="98916-109">L'app Blazor viene fornita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98916-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="98916-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="98916-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="98916-111">L'app Blazor viene posizionata in un server o un servizio Web di hosting statico, in cui non viene usato .NET per fornire l'app Blazor.</span><span class="sxs-lookup"><span data-stu-id="98916-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="98916-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un'app Webassembly blazer come applicazione secondaria IIS.</span><span class="sxs-lookup"><span data-stu-id="98916-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="98916-113">Precompressione Brotli</span><span class="sxs-lookup"><span data-stu-id="98916-113">Brotli precompression</span></span>

<span data-ttu-id="98916-114">Quando viene pubblicata un'app webassembly blazer, l'output viene precompresso usando l' [algoritmo di compressione Brotli](https://tools.ietf.org/html/rfc7932) al livello più alto per ridurre le dimensioni dell'app e rimuovere la necessità di compressione del runtime.</span><span class="sxs-lookup"><span data-stu-id="98916-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="98916-115">Per la configurazione della compressione IIS *Web. config* , vedere la sezione relativa alla [compressione IIS: Brotli e gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="98916-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="98916-116">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="98916-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="98916-117">Il routing delle richieste per i componenti della pagina in un'app webassembly Blazer non è semplice come il routing delle richieste in un server blazer, un'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="98916-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="98916-118">Si consideri un'app webassembly Blazer con due componenti:</span><span class="sxs-lookup"><span data-stu-id="98916-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="98916-119">*Main.razor* &ndash; Viene caricato nella radice dell'app e contiene un collegamento al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="98916-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="98916-120">\*About.Razor Componente \* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="98916-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="98916-121">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="98916-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="98916-122">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="98916-122">The browser makes a request.</span></span>
1. <span data-ttu-id="98916-123">Viene restituita la pagina predefinita, di solito *index.html*.</span><span class="sxs-lookup"><span data-stu-id="98916-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="98916-124">*index.html* esegue il bootstrap dell'app.</span><span class="sxs-lookup"><span data-stu-id="98916-124">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="98916-125">Viene caricato il router di Blazor e viene eseguito il rendering del componente `Main` Razor.</span><span class="sxs-lookup"><span data-stu-id="98916-125">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="98916-126">Nella pagina principale, la selezione del collegamento al componente `About` funziona nel client perché il router Blazor impedisce al browser di inviare una richiesta su Internet a `www.contoso.com` per `About` e fornisce il componente `About` sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="98916-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="98916-127">Tutte le richieste per gli endpoint interni *nell'app Webassembly Blazer* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="98916-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="98916-128">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="98916-128">The router handles the requests internally.</span></span>

<span data-ttu-id="98916-129">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="98916-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="98916-130">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="98916-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="98916-131">Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="98916-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="98916-132">Quando viene restituito *index. html* , il router Blazer dell'app accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="98916-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="98916-133">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app.</span><span class="sxs-lookup"><span data-stu-id="98916-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="98916-134">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="98916-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="98916-135">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98916-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="98916-136">Una *distribuzione ospitata* serve l'app Webassembly Blazer nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="98916-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="98916-137">L'app webassembly client Blazer viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="98916-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="98916-138">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="98916-138">The two apps are deployed together.</span></span> <span data-ttu-id="98916-139">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="98916-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="98916-140">Per una distribuzione ospitata, Visual Studio include il modello di progetto **app Webassembly Blazer** (`blazorwasm` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ) con l'opzione`-ho|--hosted` **Hosted** selezionata `dotnet new` (quando si usa il comando).</span><span class="sxs-lookup"><span data-stu-id="98916-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="98916-141">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="98916-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="98916-142">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="98916-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="98916-143">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="98916-143">Standalone deployment</span></span>

<span data-ttu-id="98916-144">Una *distribuzione autonoma* serve l'app Webassembly blazer come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="98916-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="98916-145">Qualsiasi file server statico è in grado di servire l'app Blazor.</span><span class="sxs-lookup"><span data-stu-id="98916-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="98916-146">Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="98916-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="98916-147">IIS</span><span class="sxs-lookup"><span data-stu-id="98916-147">IIS</span></span>

<span data-ttu-id="98916-148">IIS è un file server statico per app Blazor.</span><span class="sxs-lookup"><span data-stu-id="98916-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="98916-149">Per configurare IIS per ospitare Blazor, vedere [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Creare un sito Web statico in IIS).</span><span class="sxs-lookup"><span data-stu-id="98916-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="98916-150">Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="98916-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="98916-151">Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="98916-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="98916-152">web.config</span><span class="sxs-lookup"><span data-stu-id="98916-152">web.config</span></span>

<span data-ttu-id="98916-153">Quando viene pubblicato un progetto Blazor, viene creato un file *web.config* con la configurazione di IIS seguente:</span><span class="sxs-lookup"><span data-stu-id="98916-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="98916-154">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="98916-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="98916-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="98916-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="98916-156">*JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="98916-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="98916-157">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="98916-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="98916-158">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="98916-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="98916-159">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="98916-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="98916-160">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="98916-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="98916-161">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="98916-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="98916-162">Serve la sottodirectory in cui risiedono gli asset statici dell'app (*wwwroot/{Path richiesto}*).</span><span class="sxs-lookup"><span data-stu-id="98916-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="98916-163">Creare il routing di fallback SPA in modo che le richieste di risorse non di file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="98916-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="98916-164">Usare un file Web. config personalizzato</span><span class="sxs-lookup"><span data-stu-id="98916-164">Use a custom web.config</span></span>

<span data-ttu-id="98916-165">Per usare un file *Web. config* personalizzato, inserire il file *Web. config* personalizzato alla radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="98916-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="98916-166">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="98916-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="98916-167">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="98916-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="98916-168">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="98916-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="98916-169">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="98916-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="98916-170">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="98916-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="98916-171">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="98916-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="98916-172">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="98916-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="98916-173">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="98916-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="98916-174">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="98916-174">Copy the installer to the server.</span></span> <span data-ttu-id="98916-175">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="98916-175">Run the installer.</span></span> <span data-ttu-id="98916-176">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="98916-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="98916-177">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="98916-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="98916-178">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="98916-178">Configure the website</span></span>

<span data-ttu-id="98916-179">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="98916-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="98916-180">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="98916-180">The folder contains:</span></span>

* <span data-ttu-id="98916-181">Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.</span><span class="sxs-lookup"><span data-stu-id="98916-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="98916-182">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="98916-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="98916-183">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="98916-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="98916-184">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="98916-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="98916-185">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="98916-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="98916-186">Rimuovere il gestore nel file *Web. config* pubblicato dell'app Blaze aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="98916-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="98916-187">Disabilitare `<system.webServer>` l'ereditarietà della sezione radice (padre) dell'app usando un `<location>` elemento con `inheritInChildApplications` impostato su `false`:</span><span class="sxs-lookup"><span data-stu-id="98916-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="98916-188">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="98916-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="98916-189">Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.</span><span class="sxs-lookup"><span data-stu-id="98916-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="98916-190">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="98916-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="98916-191">IIS può essere configurato tramite *Web. config* per gestire le risorse del blazer compresse Brotli o gzip.</span><span class="sxs-lookup"><span data-stu-id="98916-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="98916-192">Per una configurazione di esempio, vedere [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="98916-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="98916-193">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="98916-193">Troubleshooting</span></span>

<span data-ttu-id="98916-194">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="98916-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="98916-195">Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="98916-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="98916-196">Ciò impedisce a Gestione IIS di caricare la configurazione del sito Web e al sito Web di fornire i file statici di Blazor.</span><span class="sxs-lookup"><span data-stu-id="98916-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="98916-197">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="98916-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="98916-198">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="98916-198">Azure Storage</span></span>

<span data-ttu-id="98916-199">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app blazer senza server.</span><span class="sxs-lookup"><span data-stu-id="98916-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="98916-200">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="98916-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="98916-201">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="98916-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="98916-202">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="98916-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="98916-203">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="98916-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="98916-204">I componenti di Razor e altri endpoint non basati su file non risiedono in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="98916-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="98916-205">Quando viene ricevuta una richiesta per una di queste risorse che deve essere gestita dal router Blazor, l'errore *404 - Non trovato* generato dal servizio BLOB instrada la richiesta al percorso indicato in **Percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="98916-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="98916-206">Viene restituito il BLOB *index.html* e il router Blazor carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="98916-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="98916-207">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="98916-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="98916-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="98916-208">Nginx</span></span>

<span data-ttu-id="98916-209">Il file *nginx. conf* seguente è stato semplificato per illustrare come configurare Nginx per inviare il file *index. html* ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="98916-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="98916-210">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="98916-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="98916-211">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="98916-211">Nginx in Docker</span></span>

<span data-ttu-id="98916-212">Per ospitare Blazor in Docker usando Nginx, configurare il Dockerfile per l'uso dell'immagine di Nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="98916-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="98916-213">Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="98916-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="98916-214">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="98916-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="98916-215">Apache</span><span class="sxs-lookup"><span data-stu-id="98916-215">Apache</span></span>

<span data-ttu-id="98916-216">Per distribuire un'app webassembly Blazer in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="98916-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="98916-217">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="98916-217">Create the Apache configuration file.</span></span> <span data-ttu-id="98916-218">L'esempio seguente è un file di configurazione semplificato (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="98916-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="98916-219">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="98916-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="98916-220">Inserire i file dell'app nella `/var/www/blazorapp` directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="98916-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="98916-221">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="98916-221">Restart the Apache service.</span></span>

<span data-ttu-id="98916-222">Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="98916-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="98916-223">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="98916-223">GitHub Pages</span></span>

<span data-ttu-id="98916-224">Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="98916-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="98916-225">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="98916-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="98916-226">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="98916-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="98916-227">Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*.</span><span class="sxs-lookup"><span data-stu-id="98916-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="98916-228">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="98916-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="98916-229">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="98916-229">Host configuration values</span></span>

<span data-ttu-id="98916-230">Le [app Webassembly Blazer](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="98916-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="98916-231">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="98916-231">Content root</span></span>

<span data-ttu-id="98916-232">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="98916-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="98916-233">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="98916-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="98916-234">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="98916-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="98916-235">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="98916-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="98916-236">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="98916-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="98916-237">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="98916-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="98916-238">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="98916-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="98916-239">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="98916-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="98916-240">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="98916-240">Path base</span></span>

<span data-ttu-id="98916-241">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice ( `<base>` il `href` tag è impostato su un percorso diverso `/` da per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="98916-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="98916-242">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="98916-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="98916-243">Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="98916-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98916-244">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="98916-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="98916-245">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="98916-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="98916-246">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="98916-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="98916-247">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="98916-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="98916-248">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="98916-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="98916-249">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="98916-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="98916-250">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="98916-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="98916-251">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="98916-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="98916-252">URL</span><span class="sxs-lookup"><span data-stu-id="98916-252">URLs</span></span>

<span data-ttu-id="98916-253">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="98916-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="98916-254">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="98916-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="98916-255">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="98916-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="98916-256">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="98916-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="98916-257">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="98916-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="98916-258">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="98916-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="98916-259">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="98916-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="98916-260">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="98916-260">Configure the Linker</span></span>

<span data-ttu-id="98916-261">Blazer esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio IL non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="98916-261">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="98916-262">Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="98916-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="98916-263">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="98916-263">Custom boot resource loading</span></span>

<span data-ttu-id="98916-264">Un'app webassembly blazer può essere inizializzata con `loadBootResource` la funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="98916-264">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="98916-265">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="98916-265">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="98916-266">Consentire agli utenti di caricare risorse statiche, ad esempio i dati del fuso orario o *dotnet. WASM* da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="98916-266">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="98916-267">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="98916-267">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="98916-268">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="98916-268">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="98916-269">`loadBootResource`i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="98916-269">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="98916-270">Parametro</span><span class="sxs-lookup"><span data-stu-id="98916-270">Parameter</span></span>    | <span data-ttu-id="98916-271">Descrizione</span><span class="sxs-lookup"><span data-stu-id="98916-271">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="98916-272">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="98916-272">The type of the resource.</span></span> <span data-ttu-id="98916-273">Tipi di consentiti `assembly`: `pdb`, `dotnetjs`, `dotnetwasm`,,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="98916-273">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="98916-274">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="98916-274">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="98916-275">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="98916-275">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="98916-276">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="98916-276">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="98916-277">`loadBootResource`restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="98916-277">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="98916-278">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="98916-278">URI string.</span></span> <span data-ttu-id="98916-279">Nell'esempio seguente (*wwwroot/index.html*), i file seguenti vengono serviti da una rete CDN all' `https://my-awesome-cdn.com/`Indirizzo:</span><span class="sxs-lookup"><span data-stu-id="98916-279">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="98916-280">*DotNet. \*. js*</span><span class="sxs-lookup"><span data-stu-id="98916-280">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="98916-281">*dotnet. WASM*</span><span class="sxs-lookup"><span data-stu-id="98916-281">*dotnet.wasm*</span></span>
  * <span data-ttu-id="98916-282">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="98916-282">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="98916-283">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="98916-283">`Promise<Response>`.</span></span> <span data-ttu-id="98916-284">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="98916-284">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="98916-285">L'esempio seguente (*wwwroot/index.html*) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="98916-285">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="98916-286">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="98916-286">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="98916-287">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="98916-287">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="98916-288">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="98916-288">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="98916-289">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="98916-289">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="98916-290">I tipi non specificati `loadBootResource` in vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="98916-290">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>
