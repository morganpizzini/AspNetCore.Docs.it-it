---
title: Ospitare e distribuire Blazor ASP.NET Core WebAssembly
author: guardrex
description: Informazioni su come ospitare Blazor e distribuire un'app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine GitHub.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751124"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="b5c76-103">Ospitare e distribuire Blazor ASP.NET Core WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b5c76-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="b5c76-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b5c76-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b5c76-105">Con il [ Blazor modello di hosting WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="b5c76-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="b5c76-106">L'app, Blazor le relative dipendenze e il runtime .NET vengono scaricati nel browser.</span><span class="sxs-lookup"><span data-stu-id="b5c76-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="b5c76-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="b5c76-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="b5c76-108">Sono supportate le seguenti strategie di distribuzione:</span><span class="sxs-lookup"><span data-stu-id="b5c76-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="b5c76-109">L'app Blazor è servita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5c76-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="b5c76-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="b5c76-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="b5c76-111">L'app Blazor viene inserita in un server Web o un servizio Blazor di hosting statico, in cui .NET non viene usato per servire l'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="b5c76-112">Questa strategia è trattata nella sezione [Distribuzione autonoma,](#standalone-deployment) che include informazioni sull'hosting di un'app Blazor WebAssembly come sottoapplicazione IIS.</span><span class="sxs-lookup"><span data-stu-id="b5c76-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="b5c76-113">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="b5c76-113">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="b5c76-114">Il routing delle richieste Blazor per i componenti di pagina in Blazor un'app WebAssembly non è semplice come il routing delle richieste in un'app ospitata server.</span><span class="sxs-lookup"><span data-stu-id="b5c76-114">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="b5c76-115">Si Blazor consideri un'app WebAssembly con due componenti:</span><span class="sxs-lookup"><span data-stu-id="b5c76-115">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="b5c76-116">*Main.razor* &ndash; Viene caricato nella radice dell'app e contiene un collegamento al componente `About` (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="b5c76-116">*Main.razor* &ndash; Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="b5c76-117">\*About.Razor Componente \* &ndash; `About`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-117">*About.razor* &ndash; `About` component.</span></span>

<span data-ttu-id="b5c76-118">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="b5c76-118">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="b5c76-119">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="b5c76-119">The browser makes a request.</span></span>
1. <span data-ttu-id="b5c76-120">Viene restituita la pagina predefinita, di solito *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-120">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="b5c76-121">*index.html* esegue il bootstrap dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-121">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="b5c76-122">'il router viene caricato `Main` e viene eseguito il rendering del componente Razor.</span><span class="sxs-lookup"><span data-stu-id="b5c76-122">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="b5c76-123">Nella pagina Principale, la selezione `About` del collegamento al Blazor componente funziona sul client perché il `www.contoso.com` router `About` impedisce `About` al browser di effettuare una richiesta su Internet a per e serve il componente sottoposto a rendering stesso.</span><span class="sxs-lookup"><span data-stu-id="b5c76-123">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="b5c76-124">Tutte le richieste di endpoint interni *all'interno Blazor dell'app WebAssembly* funzionano allo stesso modo: le richieste non attivano le richieste basate su browser alle risorse ospitate dal server su Internet.</span><span class="sxs-lookup"><span data-stu-id="b5c76-124">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="b5c76-125">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="b5c76-125">The router handles the requests internally.</span></span>

<span data-ttu-id="b5c76-126">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="b5c76-126">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="b5c76-127">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-127">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="b5c76-128">Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-128">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="b5c76-129">Quando viene restituito *index.html,* il Blazor router dell'app prende il sopravvento e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="b5c76-129">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="b5c76-130">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo di riscrittura URL con il file *web.config* pubblicato dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-130">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="b5c76-131">Per ulteriori informazioni, vedere la sezione [IIS.](#iis)</span><span class="sxs-lookup"><span data-stu-id="b5c76-131">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="b5c76-132">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5c76-132">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="b5c76-133">Una *distribuzione ospitata* serve l'app Blazor WebAssembly ai browser di [un'app ASP.NET Core](xref:index) in esecuzione su un server Web.</span><span class="sxs-lookup"><span data-stu-id="b5c76-133">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="b5c76-134">L'app WebAssembly client Blazor viene pubblicata nella cartella */bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="b5c76-134">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="b5c76-135">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="b5c76-135">The two apps are deployed together.</span></span> <span data-ttu-id="b5c76-136">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b5c76-136">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b5c76-137">Per una distribuzione ospitata, Visual Studio include`blazorwasm` il`-ho|--hosted` `dotnet new` \*\* Blazor \*\* modello di progetto WebAssembly App (modello quando si utilizza il comando [dotnet new)](/dotnet/core/tools/dotnet-new) con l'opzione **Hosted** selezionata (quando si utilizza il comando ).</span><span class="sxs-lookup"><span data-stu-id="b5c76-137">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="b5c76-138">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="b5c76-138">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="b5c76-139">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="b5c76-139">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="b5c76-140">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="b5c76-140">Standalone deployment</span></span>

<span data-ttu-id="b5c76-141">Una *distribuzione autonoma* serve l'app Blazor WebAssembly come set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="b5c76-141">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="b5c76-142">Qualsiasi file server statico è Blazor in grado di servire l'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-142">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="b5c76-143">Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/*</span><span class="sxs-lookup"><span data-stu-id="b5c76-143">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="b5c76-144">IIS</span><span class="sxs-lookup"><span data-stu-id="b5c76-144">IIS</span></span>

<span data-ttu-id="b5c76-145">IIS è un file Blazor server statico in grado di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="b5c76-145">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="b5c76-146">Per configurare IIS per l'hosting Blazor, vedere Creare un sito Web statico in [IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="b5c76-146">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="b5c76-147">Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-147">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="b5c76-148">Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="b5c76-148">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="b5c76-149">web.config</span><span class="sxs-lookup"><span data-stu-id="b5c76-149">web.config</span></span>

<span data-ttu-id="b5c76-150">Quando Blazor un progetto viene pubblicato, viene creato un file *web.config* con la seguente configurazione IIS:</span><span class="sxs-lookup"><span data-stu-id="b5c76-150">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="b5c76-151">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5c76-151">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="b5c76-152">*.dll (con estensione dll)* &ndash;`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="b5c76-152">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="b5c76-153">*file con estensione json* &ndash;`application/json`</span><span class="sxs-lookup"><span data-stu-id="b5c76-153">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="b5c76-154">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="b5c76-154">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="b5c76-155">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b5c76-155">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="b5c76-156">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="b5c76-156">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="b5c76-157">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5c76-157">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="b5c76-158">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="b5c76-158">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="b5c76-159">Servire la sottodirectory in cui si trovano le risorse statiche dell'app (*wwwroot/ .*</span><span class="sxs-lookup"><span data-stu-id="b5c76-159">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="b5c76-160">Creare il routing di fallback SPA in modo che le richieste di asset non file vengano reindirizzate al documento predefinito dell'app nella relativa cartella di risorse statiche (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="b5c76-160">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="b5c76-161">Utilizzare un file web.config personalizzato</span><span class="sxs-lookup"><span data-stu-id="b5c76-161">Use a custom web.config</span></span>

<span data-ttu-id="b5c76-162">Per utilizzare un file *web.config* personalizzato:</span><span class="sxs-lookup"><span data-stu-id="b5c76-162">To use a custom *web.config* file:</span></span>

1. <span data-ttu-id="b5c76-163">Inserire il file *web.config* personalizzato nella radice della cartella del progetto.</span><span class="sxs-lookup"><span data-stu-id="b5c76-163">Place the custom *web.config* file at the root of the project folder.</span></span>
1. <span data-ttu-id="b5c76-164">Aggiungere la destinazione seguente al file di progetto (*csproj*):</span><span class="sxs-lookup"><span data-stu-id="b5c76-164">Add the following target to the project file (*.csproj*):</span></span>

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> <span data-ttu-id="b5c76-165">L'utilizzo della `<IsWebConfigTransformDisabled>` proprietà `true` MSBuild impostata Blazor su non è supportato nelle app WebAssembly, come [per ASP.NET applicazioni principali distribuite in IIS](xref:host-and-deploy/iis/index#webconfig-file).</span><span class="sxs-lookup"><span data-stu-id="b5c76-165">Use of the MSBuild property `<IsWebConfigTransformDisabled>` set to `true` isn't supported in Blazor WebAssembly apps [as it is for ASP.NET Core apps deployed to IIS](xref:host-and-deploy/iis/index#webconfig-file).</span></span> <span data-ttu-id="b5c76-166">Per ulteriori informazioni, vedere [Destinazione Blazor di copia necessaria per fornire web.config WASM personalizzato (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span><span class="sxs-lookup"><span data-stu-id="b5c76-166">For more information, see [Copy target required to provide custom Blazor WASM web.config (dotnet/aspnetcore #20569)](https://github.com/dotnet/aspnetcore/issues/20569).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="b5c76-167">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="b5c76-167">Install the URL Rewrite Module</span></span>

<span data-ttu-id="b5c76-168">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="b5c76-168">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="b5c76-169">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="b5c76-169">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="b5c76-170">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="b5c76-170">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="b5c76-171">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="b5c76-171">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="b5c76-172">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="b5c76-172">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="b5c76-173">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="b5c76-173">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="b5c76-174">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="b5c76-174">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="b5c76-175">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="b5c76-175">Copy the installer to the server.</span></span> <span data-ttu-id="b5c76-176">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="b5c76-176">Run the installer.</span></span> <span data-ttu-id="b5c76-177">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="b5c76-177">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="b5c76-178">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="b5c76-178">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="b5c76-179">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="b5c76-179">Configure the website</span></span>

<span data-ttu-id="b5c76-180">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-180">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="b5c76-181">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="b5c76-181">The folder contains:</span></span>

* <span data-ttu-id="b5c76-182">Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.</span><span class="sxs-lookup"><span data-stu-id="b5c76-182">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="b5c76-183">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-183">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="b5c76-184">Host come sotto-app IIS</span><span class="sxs-lookup"><span data-stu-id="b5c76-184">Host as an IIS sub-app</span></span>

<span data-ttu-id="b5c76-185">Se un'app autonoma è ospitata come sottoapp IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="b5c76-185">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="b5c76-186">Disabilitare il gestore di ASP.NET Core Module ereditato.</span><span class="sxs-lookup"><span data-stu-id="b5c76-186">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="b5c76-187">Rimuovi il gestore nel file *web.config* pubblicato `<handlers>` Blazor dall'app aggiungendo una sezione al file:</span><span class="sxs-lookup"><span data-stu-id="b5c76-187">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="b5c76-188">Disabilita l'ereditarietà della sezione `<system.webServer>` dell'app `inheritInChildApplications` radice `false`(padre) usando un `<location>` elemento con impostato su:</span><span class="sxs-lookup"><span data-stu-id="b5c76-188">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="b5c76-189">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre a [configurare il percorso di base dell'app.](xref:host-and-deploy/blazor/index#app-base-path)</span><span class="sxs-lookup"><span data-stu-id="b5c76-189">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="b5c76-190">Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.</span><span class="sxs-lookup"><span data-stu-id="b5c76-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="b5c76-191">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="b5c76-191">Troubleshooting</span></span>

<span data-ttu-id="b5c76-192">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="b5c76-192">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="b5c76-193">Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="b5c76-193">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="b5c76-194">Ciò impedisce a Gestione IIS di caricare la Blazorconfigurazione del sito Web e al sito Web di servire i file statici.</span><span class="sxs-lookup"><span data-stu-id="b5c76-194">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="b5c76-195">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b5c76-195">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="b5c76-196">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="b5c76-196">Azure Storage</span></span>

<span data-ttu-id="b5c76-197">[L'hosting](/azure/storage/) di file Blazor statici di Archiviazione di Azure consente l'hosting di app senza server.</span><span class="sxs-lookup"><span data-stu-id="b5c76-197">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="b5c76-198">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b5c76-198">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="b5c76-199">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="b5c76-199">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="b5c76-200">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-200">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="b5c76-201">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-201">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="b5c76-202">I componenti di Razor e altri endpoint non basati su file non risiedono in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="b5c76-202">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="b5c76-203">Quando viene ricevuta una richiesta per Blazor una di queste risorse che il router deve gestire, l'errore *404 - Not Found* generato dal servizio BLOB instrada la richiesta al percorso del documento di **errore**.</span><span class="sxs-lookup"><span data-stu-id="b5c76-203">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="b5c76-204">Viene restituito il BLOB *index.html* e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="b5c76-204">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="b5c76-205">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="b5c76-205">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="b5c76-206">Nginx</span><span class="sxs-lookup"><span data-stu-id="b5c76-206">Nginx</span></span>

<span data-ttu-id="b5c76-207">Il seguente file *nginx.conf* è semplificato per mostrare come configurare Nginx per inviare il file *index.html* ogni volta che non riesce a trovare un file corrispondente su disco.</span><span class="sxs-lookup"><span data-stu-id="b5c76-207">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="b5c76-208">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="b5c76-208">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="b5c76-209">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="b5c76-209">Nginx in Docker</span></span>

<span data-ttu-id="b5c76-210">Per Blazor ospitare Docker utilizzando Nginx, impostare Dockerfile per utilizzare l'immagine Nginx basata su alpino.</span><span class="sxs-lookup"><span data-stu-id="b5c76-210">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="b5c76-211">Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="b5c76-211">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="b5c76-212">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="b5c76-212">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="b5c76-213">Apache</span><span class="sxs-lookup"><span data-stu-id="b5c76-213">Apache</span></span>

<span data-ttu-id="b5c76-214">Per distribuire Blazor un'app WebAssembly a CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="b5c76-214">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="b5c76-215">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="b5c76-215">Create the Apache configuration file.</span></span> <span data-ttu-id="b5c76-216">L'esempio seguente è un file di configurazione semplificato (*blazorapp.config*):</span><span class="sxs-lookup"><span data-stu-id="b5c76-216">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="b5c76-217">Inserire il file di configurazione `/etc/httpd/conf.d/` Apache nella directory, che è la directory di configurazione Apache predefinita in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="b5c76-217">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="b5c76-218">Inserire i file dell'app nella `/var/www/blazorapp` directory `DocumentRoot` (il percorso specificato nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="b5c76-218">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="b5c76-219">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="b5c76-219">Restart the Apache service.</span></span>

<span data-ttu-id="b5c76-220">Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="b5c76-220">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="b5c76-221">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="b5c76-221">GitHub Pages</span></span>

<span data-ttu-id="b5c76-222">Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-222">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="b5c76-223">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="b5c76-223">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="b5c76-224">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="b5c76-224">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="b5c76-225">Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-225">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="b5c76-226">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-226">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b5c76-227">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="b5c76-227">Host configuration values</span></span>

<span data-ttu-id="b5c76-228">Le app WebAssembly possono accettare i seguenti valori di configurazione host come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="b5c76-228">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="b5c76-229">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="b5c76-229">Content root</span></span>

<span data-ttu-id="b5c76-230">L'argomento `--contentroot` imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ( radice del[contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="b5c76-230">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="b5c76-231">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-231">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="b5c76-232">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b5c76-232">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b5c76-233">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="b5c76-233">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="b5c76-234">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b5c76-234">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b5c76-235">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-235">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="b5c76-236">In Visual Studio specificare l'argomento in **Proprietà** > **degli argomenti dell'applicazione\*\*\*\*di debug** > .</span><span class="sxs-lookup"><span data-stu-id="b5c76-236">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b5c76-237">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-237">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="b5c76-238">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="b5c76-238">Path base</span></span>

<span data-ttu-id="b5c76-239">L'argomento `--pathbase` imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` quello per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="b5c76-239">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="b5c76-240">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="b5c76-240">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="b5c76-241">Per ulteriori informazioni, vedere Percorso di [base dell'app](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="b5c76-241">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5c76-242">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-242">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="b5c76-243">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="b5c76-243">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="b5c76-244">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b5c76-244">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b5c76-245">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="b5c76-245">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="b5c76-246">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b5c76-246">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b5c76-247">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-247">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="b5c76-248">In Visual Studio specificare l'argomento in **Proprietà** > **degli argomenti dell'applicazione\*\*\*\*di debug** > .</span><span class="sxs-lookup"><span data-stu-id="b5c76-248">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b5c76-249">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-249">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="b5c76-250">URL</span><span class="sxs-lookup"><span data-stu-id="b5c76-250">URLs</span></span>

<span data-ttu-id="b5c76-251">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="b5c76-251">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="b5c76-252">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="b5c76-252">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="b5c76-253">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="b5c76-253">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="b5c76-254">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="b5c76-254">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="b5c76-255">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b5c76-255">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="b5c76-256">In Visual Studio specificare l'argomento in **Proprietà** > **degli argomenti dell'applicazione\*\*\*\*di debug** > .</span><span class="sxs-lookup"><span data-stu-id="b5c76-256">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="b5c76-257">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b5c76-257">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="b5c76-258">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="b5c76-258">Configure the Linker</span></span>

Blazor<span data-ttu-id="b5c76-259">esegue il collegamento in linguaggio intermedio (IL) in ogni build di rilascio per rimuovere il linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="b5c76-259"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="b5c76-260">Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="b5c76-260">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>
