---
title: Ospitare e distribuire ASP.NET Core Blazor Webassembly
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84239385"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="0a246-103">Ospitare e distribuire ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="0a246-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="0a246-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="0a246-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="0a246-105">Con il [ Blazor modello di hosting webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="0a246-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="0a246-106">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="0a246-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="0a246-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="0a246-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="0a246-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="0a246-109">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a246-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="0a246-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="0a246-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="0a246-111">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="0a246-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="0a246-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor app webassembly come una sub-app IIS.</span><span class="sxs-lookup"><span data-stu-id="0a246-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="precompression"></a><span data-ttu-id="0a246-113">Precompressione</span><span class="sxs-lookup"><span data-stu-id="0a246-113">Precompression</span></span>

<span data-ttu-id="0a246-114">Quando Blazor viene pubblicata un'app webassembly, l'output viene precompresso per ridurre le dimensioni dell'app e rimuovere la necessità di compressione del runtime.</span><span class="sxs-lookup"><span data-stu-id="0a246-114">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span> <span data-ttu-id="0a246-115">Vengono utilizzati gli algoritmi di compressione seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="0a246-116">[Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)</span><span class="sxs-lookup"><span data-stu-id="0a246-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="0a246-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="0a246-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="0a246-118">Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :</span><span class="sxs-lookup"><span data-stu-id="0a246-118">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="0a246-119">Per la configurazione della compressione IIS *Web. config* , vedere la sezione relativa alla [compressione IIS: Brotli e gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="0a246-119">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="0a246-120">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="0a246-120">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="0a246-121">Il routing delle richieste per i componenti della pagina in un' Blazor app webassembly non è semplice come il routing delle richieste in un Blazor Server, un'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="0a246-121">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="0a246-122">Si consideri un' Blazor app webassembly con due componenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-122">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="0a246-123">*Main. Razor*: carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="0a246-123">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="0a246-124">*About. Razor*: `About` Component.</span><span class="sxs-lookup"><span data-stu-id="0a246-124">*About.razor*: `About` component.</span></span>

<span data-ttu-id="0a246-125">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="0a246-125">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="0a246-126">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="0a246-126">The browser makes a request.</span></span>
1. <span data-ttu-id="0a246-127">Viene restituita la pagina predefinita, di solito *index.html*.</span><span class="sxs-lookup"><span data-stu-id="0a246-127">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="0a246-128">*index.html* esegue il bootstrap dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-128">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="0a246-129">il router viene caricato e il componente viene sottoposto a Razor `Main` rendering.</span><span class="sxs-lookup"><span data-stu-id="0a246-129">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="0a246-130">Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` .</span><span class="sxs-lookup"><span data-stu-id="0a246-130">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="0a246-131">Tutte le richieste per gli endpoint interni *nell' Blazor app webassembly* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="0a246-131">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="0a246-132">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="0a246-132">The router handles the requests internally.</span></span>

<span data-ttu-id="0a246-133">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="0a246-133">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="0a246-134">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="0a246-134">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="0a246-135">Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="0a246-135">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="0a246-136">Quando viene restituito *index. html* , il router dell'app Blazor accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="0a246-136">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="0a246-137">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-137">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="0a246-138">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="0a246-138">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="0a246-139">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a246-139">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="0a246-140">Una *distribuzione ospitata* serve l' Blazor app webassembly nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="0a246-140">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="0a246-141">L' Blazor app webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="0a246-141">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="0a246-142">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="0a246-142">The two apps are deployed together.</span></span> <span data-ttu-id="0a246-143">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a246-143">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="0a246-144">Per una distribuzione ospitata, Visual Studio include il modello di progetto di \*\* Blazor app webassembly\*\* ( `blazorwasm` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ) con l'opzione **Hosted** selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="0a246-144">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="0a246-145">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="0a246-145">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="0a246-146">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="0a246-146">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="0a246-147">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="0a246-147">Standalone deployment</span></span>

<span data-ttu-id="0a246-148">Una *distribuzione autonoma* serve l' Blazor app webassembly come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="0a246-148">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="0a246-149">Qualsiasi file server statico è in grado di gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="0a246-149">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="0a246-150">Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="0a246-150">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="0a246-151">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="0a246-151">Azure App Service</span></span>

Blazor<span data-ttu-id="0a246-152">Le app webassembly possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="0a246-152"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="0a246-153">La distribuzione Blazor di un'app webassembly autonoma nel servizio app Azure per Linux non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="0a246-153">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="0a246-154">Un'immagine server Linux per ospitare l'app non è disponibile in questo momento.</span><span class="sxs-lookup"><span data-stu-id="0a246-154">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="0a246-155">Il lavoro è in corso per abilitare questo scenario.</span><span class="sxs-lookup"><span data-stu-id="0a246-155">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="0a246-156">IIS</span><span class="sxs-lookup"><span data-stu-id="0a246-156">IIS</span></span>

<span data-ttu-id="0a246-157">IIS è un file server statico idoneo per le Blazor app.</span><span class="sxs-lookup"><span data-stu-id="0a246-157">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="0a246-158">Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="0a246-158">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="0a246-159">Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="0a246-159">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="0a246-160">Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="0a246-160">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="0a246-161">web.config</span><span class="sxs-lookup"><span data-stu-id="0a246-161">web.config</span></span>

<span data-ttu-id="0a246-162">Quando Blazor viene pubblicato un progetto, viene creato un file *Web. config* con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="0a246-162">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="0a246-163">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-163">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="0a246-164">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="0a246-164">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="0a246-165">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="0a246-165">*.json*: `application/json`</span></span>
  * <span data-ttu-id="0a246-166">*. WASM*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="0a246-166">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="0a246-167">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="0a246-167">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="0a246-168">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="0a246-168">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="0a246-169">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-169">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="0a246-170">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="0a246-170">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="0a246-171">Serve la sottodirectory in cui risiedono gli asset statici dell'app (*wwwroot/{Path richiesto}*).</span><span class="sxs-lookup"><span data-stu-id="0a246-171">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="0a246-172">Creare il routing di fallback SPA in modo che le richieste di risorse non di file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="0a246-172">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="0a246-173">Usare un file Web. config personalizzato</span><span class="sxs-lookup"><span data-stu-id="0a246-173">Use a custom web.config</span></span>

<span data-ttu-id="0a246-174">Per usare un file *Web. config* personalizzato, inserire il file *Web. config* personalizzato alla radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="0a246-174">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="0a246-175">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="0a246-175">Install the URL Rewrite Module</span></span>

<span data-ttu-id="0a246-176">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="0a246-176">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="0a246-177">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="0a246-177">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="0a246-178">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="0a246-178">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="0a246-179">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="0a246-179">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="0a246-180">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="0a246-180">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="0a246-181">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="0a246-181">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="0a246-182">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="0a246-182">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="0a246-183">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="0a246-183">Copy the installer to the server.</span></span> <span data-ttu-id="0a246-184">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="0a246-184">Run the installer.</span></span> <span data-ttu-id="0a246-185">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="0a246-185">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="0a246-186">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="0a246-186">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="0a246-187">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="0a246-187">Configure the website</span></span>

<span data-ttu-id="0a246-188">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-188">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="0a246-189">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="0a246-189">The folder contains:</span></span>

* <span data-ttu-id="0a246-190">Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.</span><span class="sxs-lookup"><span data-stu-id="0a246-190">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="0a246-191">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-191">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="0a246-192">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="0a246-192">Host as an IIS sub-app</span></span>

<span data-ttu-id="0a246-193">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-193">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="0a246-194">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="0a246-194">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="0a246-195">Rimuovere il gestore nel Blazor file *Web. config* pubblicato dall'app aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="0a246-195">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="0a246-196">Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :</span><span class="sxs-lookup"><span data-stu-id="0a246-196">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="0a246-197">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="0a246-197">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="0a246-198">Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.</span><span class="sxs-lookup"><span data-stu-id="0a246-198">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="0a246-199">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="0a246-199">Brotli and Gzip compression</span></span>

<span data-ttu-id="0a246-200">IIS può essere configurato tramite *Web. config* per gestire gli asset compressi Brotli o gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="0a246-200">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="0a246-201">Per una configurazione di esempio, vedere [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="0a246-201">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="0a246-202">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="0a246-202">Troubleshooting</span></span>

<span data-ttu-id="0a246-203">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="0a246-203">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="0a246-204">Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="0a246-204">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="0a246-205">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="0a246-205">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="0a246-206">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="0a246-206">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="0a246-207">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="0a246-207">Azure Storage</span></span>

<span data-ttu-id="0a246-208">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor .</span><span class="sxs-lookup"><span data-stu-id="0a246-208">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="0a246-209">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0a246-209">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="0a246-210">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="0a246-210">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="0a246-211">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="0a246-211">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="0a246-212">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="0a246-212">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="0a246-213">i componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="0a246-213"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="0a246-214">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="0a246-214">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="0a246-215">Viene restituito il BLOB *index. html* e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="0a246-215">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="0a246-216">Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-216">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="0a246-217">Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.</span><span class="sxs-lookup"><span data-stu-id="0a246-217">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="0a246-218">Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-218">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="0a246-219">In Storage Explorer (portale di Azure) per ogni file:</span><span class="sxs-lookup"><span data-stu-id="0a246-219">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="0a246-220">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="0a246-220">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="0a246-221">Impostare **ContentType** e selezionare il pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="0a246-221">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="0a246-222">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="0a246-222">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="0a246-223">Nginx</span><span class="sxs-lookup"><span data-stu-id="0a246-223">Nginx</span></span>

<span data-ttu-id="0a246-224">Il file *nginx. conf* seguente è stato semplificato per illustrare come configurare Nginx per inviare il file *index. html* ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="0a246-224">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="0a246-225">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="0a246-225">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="0a246-226">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="0a246-226">Nginx in Docker</span></span>

<span data-ttu-id="0a246-227">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="0a246-227">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="0a246-228">Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="0a246-228">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="0a246-229">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="0a246-229">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="0a246-230">Apache</span><span class="sxs-lookup"><span data-stu-id="0a246-230">Apache</span></span>

<span data-ttu-id="0a246-231">Per distribuire un' Blazor app webassembly in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="0a246-231">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="0a246-232">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="0a246-232">Create the Apache configuration file.</span></span> <span data-ttu-id="0a246-233">L'esempio seguente è un file di configurazione semplificato (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="0a246-233">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="0a246-234">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="0a246-234">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="0a246-235">Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="0a246-235">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="0a246-236">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="0a246-236">Restart the Apache service.</span></span>

<span data-ttu-id="0a246-237">Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="0a246-237">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="0a246-238">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="0a246-238">GitHub Pages</span></span>

<span data-ttu-id="0a246-239">Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="0a246-239">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="0a246-240">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="0a246-240">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="0a246-241">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="0a246-241">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="0a246-242">Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*.</span><span class="sxs-lookup"><span data-stu-id="0a246-242">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="0a246-243">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="0a246-243">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="0a246-244">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="0a246-244">Host configuration values</span></span>

<span data-ttu-id="0a246-245">Le [ Blazor app webassembly](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="0a246-245">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="0a246-246">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="0a246-246">Content root</span></span>

<span data-ttu-id="0a246-247">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="0a246-247">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="0a246-248">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-248">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="0a246-249">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="0a246-249">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="0a246-250">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="0a246-250">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="0a246-251">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="0a246-251">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="0a246-252">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0a246-252">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="0a246-253">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="0a246-253">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="0a246-254">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0a246-254">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="0a246-255">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="0a246-255">Path base</span></span>

<span data-ttu-id="0a246-256">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="0a246-256">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="0a246-257">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="0a246-257">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="0a246-258">Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="0a246-258">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0a246-259">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="0a246-259">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="0a246-260">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="0a246-260">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="0a246-261">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="0a246-261">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="0a246-262">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="0a246-262">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="0a246-263">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="0a246-263">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="0a246-264">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0a246-264">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="0a246-265">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="0a246-265">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="0a246-266">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0a246-266">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="0a246-267">URL</span><span class="sxs-lookup"><span data-stu-id="0a246-267">URLs</span></span>

<span data-ttu-id="0a246-268">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="0a246-268">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="0a246-269">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="0a246-269">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="0a246-270">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="0a246-270">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="0a246-271">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="0a246-271">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="0a246-272">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0a246-272">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="0a246-273">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="0a246-273">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="0a246-274">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0a246-274">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="0a246-275">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="0a246-275">Configure the Linker</span></span>

Blazor<span data-ttu-id="0a246-276">esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="0a246-276"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="0a246-277">Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="0a246-277">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="0a246-278">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="0a246-278">Custom boot resource loading</span></span>

<span data-ttu-id="0a246-279">Un' Blazor app webassembly può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="0a246-279">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="0a246-280">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-280">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="0a246-281">Consentire agli utenti di caricare risorse statiche, ad esempio i dati del fuso orario o *dotnet. WASM* da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="0a246-281">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="0a246-282">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="0a246-282">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="0a246-283">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="0a246-283">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="0a246-284">`loadBootResource`i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="0a246-284">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="0a246-285">Parametro</span><span class="sxs-lookup"><span data-stu-id="0a246-285">Parameter</span></span>    | <span data-ttu-id="0a246-286">Descrizione</span><span class="sxs-lookup"><span data-stu-id="0a246-286">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="0a246-287">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="0a246-287">The type of the resource.</span></span> <span data-ttu-id="0a246-288">Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="0a246-288">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="0a246-289">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="0a246-289">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="0a246-290">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="0a246-290">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="0a246-291">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="0a246-291">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="0a246-292">`loadBootResource`restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="0a246-292">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="0a246-293">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="0a246-293">URI string.</span></span> <span data-ttu-id="0a246-294">Nell'esempio seguente (*wwwroot/index.html*), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="0a246-294">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="0a246-295">*dotnet. \* .. JS*</span><span class="sxs-lookup"><span data-stu-id="0a246-295">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="0a246-296">*dotnet. WASM*</span><span class="sxs-lookup"><span data-stu-id="0a246-296">*dotnet.wasm*</span></span>
  * <span data-ttu-id="0a246-297">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="0a246-297">Timezone data</span></span>

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

* <span data-ttu-id="0a246-298">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="0a246-298">`Promise<Response>`.</span></span> <span data-ttu-id="0a246-299">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="0a246-299">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="0a246-300">L'esempio seguente (*wwwroot/index.html*) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="0a246-300">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="0a246-301">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="0a246-301">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="0a246-302">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="0a246-302">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="0a246-303">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="0a246-303">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="0a246-304">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="0a246-304">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="0a246-305">I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="0a246-305">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="0a246-306">Modificare l'estensione di file DLL</span><span class="sxs-lookup"><span data-stu-id="0a246-306">Change the filename extension of DLL files</span></span>

<span data-ttu-id="0a246-307">Se è necessario modificare le estensioni del nome file dei file con *estensione dll* pubblicati dell'app, seguire le istruzioni riportate in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="0a246-307">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="0a246-308">Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare i file con estensione *dll* per usare un'estensione di file diversa.</span><span class="sxs-lookup"><span data-stu-id="0a246-308">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="0a246-309">Specificare come destinazione i file con *estensione dll* nella directory *wwwroot* dell'output pubblicato dell'app (ad esempio, *{Content root}/bin/Release/netstandard2.1/Publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="0a246-309">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="0a246-310">Negli esempi seguenti, i file con estensione *dll* vengono rinominati per l'utilizzo dell'estensione di file *bin* .</span><span class="sxs-lookup"><span data-stu-id="0a246-310">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="0a246-311">In Windows:</span><span class="sxs-lookup"><span data-stu-id="0a246-311">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="0a246-312">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0a246-312">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="0a246-313">In Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="0a246-313">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="0a246-314">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0a246-314">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="0a246-315">Per usare un'estensione di file diversa da *. bin*, sostituire *. bin* nei comandi precedenti.</span><span class="sxs-lookup"><span data-stu-id="0a246-315">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="0a246-316">Per risolvere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="0a246-316">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="0a246-317">Rimuovere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* .</span><span class="sxs-lookup"><span data-stu-id="0a246-317">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="0a246-318">La compressione è disabilitata con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="0a246-318">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="0a246-319">Ricomprimere il file *blazer. boot. JSON* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="0a246-319">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="0a246-320">Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso.</span><span class="sxs-lookup"><span data-stu-id="0a246-320">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="0a246-321">Rimuovere o ricomprimere *wwwroot/service-worker-assets. js. br* e *wwwroot/service-worker-assets. js. gz*.</span><span class="sxs-lookup"><span data-stu-id="0a246-321">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="0a246-322">In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.</span><span class="sxs-lookup"><span data-stu-id="0a246-322">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="0a246-323">Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="0a246-323">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="0a246-324">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="0a246-324">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="0a246-325">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="0a246-325">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="0a246-326">Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="0a246-326">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="0a246-327">Per fornire commenti e suggerimenti, vedere [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="0a246-327">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
