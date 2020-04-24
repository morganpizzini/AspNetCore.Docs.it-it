---
title: Ospitare e distribuire ASP.NET Core Blazor webassembly
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: daaaab360e93de1cf10feec2db21d3acc25920bd
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110876"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="fb33d-103">Ospitare e distribuire ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="fb33d-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="fb33d-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27)e [ben Adams](https://twitter.com/ben_a_adams).</span><span class="sxs-lookup"><span data-stu-id="fb33d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), and [Ben Adams](https://twitter.com/ben_a_adams).</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fb33d-105">Con il [ Blazor modello di hosting webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="fb33d-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="fb33d-106">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="fb33d-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="fb33d-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="fb33d-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="fb33d-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="fb33d-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="fb33d-109">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb33d-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="fb33d-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="fb33d-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="fb33d-111">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non Blazor viene usato per gestire l'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="fb33d-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni Blazor sull'hosting di un'app webassembly come una sub-app IIS.</span><span class="sxs-lookup"><span data-stu-id="fb33d-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="brotli-precompression"></a><span data-ttu-id="fb33d-113">Precompressione Brotli</span><span class="sxs-lookup"><span data-stu-id="fb33d-113">Brotli precompression</span></span>

<span data-ttu-id="fb33d-114">Quando viene Blazor pubblicata un'app webassembly, l'output viene precompresso usando l' [algoritmo di compressione Brotli](https://tools.ietf.org/html/rfc7932) al livello più alto per ridurre le dimensioni dell'app e rimuovere la necessità di compressione del runtime.</span><span class="sxs-lookup"><span data-stu-id="fb33d-114">When a Blazor WebAssembly app is published, the output is precompressed using the [Brotli compression algorithm](https://tools.ietf.org/html/rfc7932) at the highest level to reduce the app size and remove the need for runtime compression.</span></span>

<span data-ttu-id="fb33d-115">Per la configurazione della compressione IIS *Web. config* , vedere la sezione relativa alla [compressione IIS: Brotli e gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="fb33d-115">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="fb33d-116">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="fb33d-116">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="fb33d-117">Il routing delle richieste per i componenti Blazor della pagina in un'app webassembly non è semplice come Blazor il routing delle richieste in un server, un'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="fb33d-117">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="fb33d-118">Si consideri un' Blazor app webassembly con due componenti:</span><span class="sxs-lookup"><span data-stu-id="fb33d-118">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="fb33d-119">*Main.razor* &ndash; Viene caricato nella radice dell'app e contiene un collegamento al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="fb33d-119">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="fb33d-120">\*About.Razor Componente \* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-120">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="fb33d-121">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="fb33d-121">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="fb33d-122">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="fb33d-122">The browser makes a request.</span></span>
1. <span data-ttu-id="fb33d-123">Viene restituita la pagina predefinita, di solito *index.html*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-123">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="fb33d-124">*index.html* esegue il bootstrap dell'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-124">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="fb33d-125">il router viene caricato e viene eseguito `Main` il rendering del componente Razor.</span><span class="sxs-lookup"><span data-stu-id="fb33d-125">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="fb33d-126">Nella pagina principale, selezionando `About` il collegamento al componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet `www.contoso.com` per `About` e serve il componente di cui è stato `About` eseguito il rendering.</span><span class="sxs-lookup"><span data-stu-id="fb33d-126">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="fb33d-127">Tutte le richieste per gli endpoint interni *nell' Blazor app webassembly* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="fb33d-127">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="fb33d-128">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="fb33d-128">The router handles the requests internally.</span></span>

<span data-ttu-id="fb33d-129">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="fb33d-129">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="fb33d-130">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-130">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="fb33d-131">Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-131">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="fb33d-132">Quando viene restituito *index. html* , il Blazor router dell'app accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="fb33d-132">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="fb33d-133">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-133">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="fb33d-134">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="fb33d-134">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="fb33d-135">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb33d-135">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="fb33d-136">Una *distribuzione ospitata* serve Blazor l'app webassembly nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="fb33d-136">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="fb33d-137">L'app Blazor webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="fb33d-137">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="fb33d-138">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="fb33d-138">The two apps are deployed together.</span></span> <span data-ttu-id="fb33d-139">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fb33d-139">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="fb33d-140">Per una distribuzione ospitata, Visual Studio include`-ho|--hosted` il `dotnet new` `blazorwasm` [dotnet new](/dotnet/core/tools/dotnet-new) **Hosted** \*\* Blazor \*\* modello di progetto di app webassembly (modello quando si usa il comando DotNet New) con l'opzione Hosted selezionata (quando si usa il comando).</span><span class="sxs-lookup"><span data-stu-id="fb33d-140">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="fb33d-141">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="fb33d-141">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="fb33d-142">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="fb33d-142">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="fb33d-143">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="fb33d-143">Standalone deployment</span></span>

<span data-ttu-id="fb33d-144">Una *distribuzione autonoma* serve Blazor l'app webassembly come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="fb33d-144">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="fb33d-145">Qualsiasi file server statico è in grado di gestire Blazor l'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-145">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="fb33d-146">Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="fb33d-146">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="fb33d-147">IIS</span><span class="sxs-lookup"><span data-stu-id="fb33d-147">IIS</span></span>

<span data-ttu-id="fb33d-148">IIS è un file server statico idoneo Blazor per le app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-148">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="fb33d-149">Per configurare IIS per l' Blazorhosting, vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="fb33d-149">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="fb33d-150">Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-150">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="fb33d-151">Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="fb33d-151">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="fb33d-152">web.config</span><span class="sxs-lookup"><span data-stu-id="fb33d-152">web.config</span></span>

<span data-ttu-id="fb33d-153">Quando viene Blazor pubblicato un progetto, viene creato un file *Web. config* con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="fb33d-153">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="fb33d-154">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="fb33d-154">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="fb33d-155">*. dll* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="fb33d-155">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="fb33d-156">*JSON* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="fb33d-156">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="fb33d-157">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="fb33d-157">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="fb33d-158">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="fb33d-158">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="fb33d-159">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="fb33d-159">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="fb33d-160">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="fb33d-160">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="fb33d-161">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="fb33d-161">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="fb33d-162">Serve la sottodirectory in cui risiedono gli asset statici dell'app (*wwwroot/{Path richiesto}*).</span><span class="sxs-lookup"><span data-stu-id="fb33d-162">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="fb33d-163">Creare il routing di fallback SPA in modo che le richieste di risorse non di file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="fb33d-163">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="fb33d-164">Usare un file Web. config personalizzato</span><span class="sxs-lookup"><span data-stu-id="fb33d-164">Use a custom web.config</span></span>

<span data-ttu-id="fb33d-165">Per usare un file *Web. config* personalizzato, inserire il file *Web. config* personalizzato alla radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="fb33d-165">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="fb33d-166">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="fb33d-166">Install the URL Rewrite Module</span></span>

<span data-ttu-id="fb33d-167">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="fb33d-167">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="fb33d-168">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="fb33d-168">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="fb33d-169">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="fb33d-169">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="fb33d-170">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="fb33d-170">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="fb33d-171">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="fb33d-171">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="fb33d-172">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="fb33d-172">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="fb33d-173">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="fb33d-173">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="fb33d-174">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="fb33d-174">Copy the installer to the server.</span></span> <span data-ttu-id="fb33d-175">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="fb33d-175">Run the installer.</span></span> <span data-ttu-id="fb33d-176">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="fb33d-176">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="fb33d-177">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="fb33d-177">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="fb33d-178">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="fb33d-178">Configure the website</span></span>

<span data-ttu-id="fb33d-179">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-179">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="fb33d-180">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="fb33d-180">The folder contains:</span></span>

* <span data-ttu-id="fb33d-181">Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.</span><span class="sxs-lookup"><span data-stu-id="fb33d-181">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="fb33d-182">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-182">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="fb33d-183">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="fb33d-183">Host as an IIS sub-app</span></span>

<span data-ttu-id="fb33d-184">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="fb33d-184">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="fb33d-185">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="fb33d-185">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="fb33d-186">Rimuovere il gestore nel file Blazor *Web. config* pubblicato dall'app aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="fb33d-186">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="fb33d-187">Disabilitare `<system.webServer>` l'ereditarietà della sezione radice (padre) dell'app usando un `<location>` elemento con `inheritInChildApplications` impostato su `false`:</span><span class="sxs-lookup"><span data-stu-id="fb33d-187">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="fb33d-188">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="fb33d-188">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="fb33d-189">Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.</span><span class="sxs-lookup"><span data-stu-id="fb33d-189">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="fb33d-190">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="fb33d-190">Brotli and Gzip compression</span></span>

<span data-ttu-id="fb33d-191">IIS può essere configurato tramite *Web. config* per gestire gli asset compressi Blazor Brotli o gzip.</span><span class="sxs-lookup"><span data-stu-id="fb33d-191">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="fb33d-192">Per una configurazione di esempio, vedere [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="fb33d-192">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="fb33d-193">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="fb33d-193">Troubleshooting</span></span>

<span data-ttu-id="fb33d-194">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="fb33d-194">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="fb33d-195">Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="fb33d-195">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="fb33d-196">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e Blazoril sito Web dai file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="fb33d-196">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="fb33d-197">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="fb33d-197">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="fb33d-198">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="fb33d-198">Azure Storage</span></span>

<span data-ttu-id="fb33d-199">L'hosting di file statici di [archiviazione](/azure/storage/) di Blazor Azure consente l'hosting di app senza server.</span><span class="sxs-lookup"><span data-stu-id="fb33d-199">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="fb33d-200">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fb33d-200">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="fb33d-201">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="fb33d-201">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="fb33d-202">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-202">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="fb33d-203">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-203">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="fb33d-204">I componenti di Razor e altri endpoint non basati su file non risiedono in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="fb33d-204">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="fb33d-205">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-205">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="fb33d-206">Viene restituito il BLOB *index. html* e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="fb33d-206">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="fb33d-207">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="fb33d-207">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="fb33d-208">Nginx</span><span class="sxs-lookup"><span data-stu-id="fb33d-208">Nginx</span></span>

<span data-ttu-id="fb33d-209">Il file *nginx. conf* seguente è stato semplificato per illustrare come configurare Nginx per inviare il file *index. html* ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="fb33d-209">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="fb33d-210">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="fb33d-210">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="fb33d-211">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="fb33d-211">Nginx in Docker</span></span>

<span data-ttu-id="fb33d-212">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="fb33d-212">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="fb33d-213">Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="fb33d-213">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="fb33d-214">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="fb33d-214">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="fb33d-215">Apache</span><span class="sxs-lookup"><span data-stu-id="fb33d-215">Apache</span></span>

<span data-ttu-id="fb33d-216">Per distribuire un' Blazor app webassembly in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="fb33d-216">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="fb33d-217">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="fb33d-217">Create the Apache configuration file.</span></span> <span data-ttu-id="fb33d-218">L'esempio seguente è un file di configurazione semplificato (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="fb33d-218">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="fb33d-219">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="fb33d-219">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="fb33d-220">Inserire i file dell'app nella `/var/www/blazorapp` directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="fb33d-220">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="fb33d-221">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="fb33d-221">Restart the Apache service.</span></span>

<span data-ttu-id="fb33d-222">Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="fb33d-222">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="fb33d-223">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="fb33d-223">GitHub Pages</span></span>

<span data-ttu-id="fb33d-224">Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-224">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="fb33d-225">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="fb33d-225">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="fb33d-226">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="fb33d-226">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="fb33d-227">Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-227">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="fb33d-228">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-228">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="fb33d-229">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="fb33d-229">Host configuration values</span></span>

<span data-ttu-id="fb33d-230">Le app webassembly possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="fb33d-230">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="fb33d-231">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="fb33d-231">Content root</span></span>

<span data-ttu-id="fb33d-232">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="fb33d-232">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="fb33d-233">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-233">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="fb33d-234">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fb33d-234">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fb33d-235">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="fb33d-235">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="fb33d-236">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-236">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fb33d-237">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-237">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="fb33d-238">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-238">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fb33d-239">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-239">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="fb33d-240">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="fb33d-240">Path base</span></span>

<span data-ttu-id="fb33d-241">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice ( `<base>` il `href` tag è impostato su un percorso diverso `/` da per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="fb33d-241">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="fb33d-242">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="fb33d-242">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="fb33d-243">Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="fb33d-243">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="fb33d-244">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-244">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="fb33d-245">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="fb33d-245">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="fb33d-246">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fb33d-246">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fb33d-247">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="fb33d-247">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="fb33d-248">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-248">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fb33d-249">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-249">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="fb33d-250">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-250">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fb33d-251">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-251">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="fb33d-252">URL</span><span class="sxs-lookup"><span data-stu-id="fb33d-252">URLs</span></span>

<span data-ttu-id="fb33d-253">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="fb33d-253">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="fb33d-254">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="fb33d-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="fb33d-255">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="fb33d-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="fb33d-256">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="fb33d-257">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="fb33d-257">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="fb33d-258">In Visual Studio specificare l'argomento in **Proprietà** > **debug** > **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="fb33d-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="fb33d-259">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="fb33d-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="fb33d-260">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="fb33d-260">Configure the Linker</span></span>

Blazor<span data-ttu-id="fb33d-261">esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="fb33d-261"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="fb33d-262">Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="fb33d-262">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
