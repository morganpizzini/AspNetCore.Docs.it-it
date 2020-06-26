---
title: Ospitare e distribuire ASP.NET CoreBlazor WebAssembly
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 47ba6f54c68158b3f6dcbbdda06ec8747cf88241
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402546"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="bf8d7-103">Ospitare e distribuire ASP.NET CoreBlazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="bf8d7-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="bf8d7-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="bf8d7-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="bf8d7-105">Con il [ Blazor WebAssembly modello di hosting](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="bf8d7-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="bf8d7-106">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="bf8d7-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="bf8d7-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="bf8d7-109">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="bf8d7-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="bf8d7-111">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="bf8d7-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor WebAssembly app come app secondaria IIS.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="bf8d7-113">Compressione</span><span class="sxs-lookup"><span data-stu-id="bf8d7-113">Compression</span></span>

<span data-ttu-id="bf8d7-114">Quando Blazor WebAssembly viene pubblicata un'app, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="bf8d7-115">Vengono utilizzati gli algoritmi di compressione seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="bf8d7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)</span><span class="sxs-lookup"><span data-stu-id="bf8d7-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="bf8d7-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="bf8d7-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

Blazor<span data-ttu-id="bf8d7-118">si basa sull'host per gestire i file compressi appropriati.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-118"> relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="bf8d7-119">Quando si usa un ASP.NET Core progetto ospitato, il progetto host è in grado di eseguire la negoziazione del contenuto e di servire i file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="bf8d7-120">Quando si ospita un' Blazor WebAssembly app autonoma, potrebbe essere necessario lavoro aggiuntivo per garantire che vengano serviti file compressi in modo statico:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="bf8d7-121">Per `web.config` la configurazione della compressione IIS, vedere la sezione [IIS: Brotli e la compressione gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="bf8d7-122">Quando si ospitano soluzioni di hosting statiche che non supportano la negoziazione del contenuto di file compressi in modo statico, ad esempio pagine di GitHub, è consigliabile configurare l'app per recuperare e decodificare i file compressi Brotli:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="bf8d7-123">Fare riferimento al decodificatore Brotli dal [repository GitHub Google/Brotli](https://github.com/google/brotli/) nell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-123">Reference the Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli/) in the app.</span></span>
  * <span data-ttu-id="bf8d7-124">Aggiornare l'app per l'uso del decodificatore.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-124">Update the app to use the decoder.</span></span> <span data-ttu-id="bf8d7-125">Modificare il markup all'interno del `<body>` tag di chiusura in `wwwroot/index.html` come segue:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-125">Change the markup inside the the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="brotli.decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
          return (async function () {
            const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
            if (!response.ok) {
              throw new Error(response.statusText);
            }
            const originalResponseBuffer = await response.arrayBuffer();
            const originalResponseArray = new Int8Array(originalResponseBuffer);
            const decompressedResponseArray = BrotliDecode(originalResponseArray);
            const contentType = type === 
          'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
            return new Response(decompressedResponseArray, 
          { headers: { 'content-type': contentType } });
          })();
        }
      }
    });
  </script>
  ```
   
<span data-ttu-id="bf8d7-126">Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :</span><span class="sxs-lookup"><span data-stu-id="bf8d7-126">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="bf8d7-127">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="bf8d7-127">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="bf8d7-128">Il routing delle richieste per i componenti della pagina in un' Blazor WebAssembly app non è semplice come il routing delle richieste in un' Blazor Server app ospitata.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-128">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="bf8d7-129">Si consideri un' Blazor WebAssembly app con due componenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-129">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="bf8d7-130">`Main.razor`: Carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-130">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="bf8d7-131">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-131">`About.razor`: `About` component.</span></span>

<span data-ttu-id="bf8d7-132">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="bf8d7-132">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="bf8d7-133">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-133">The browser makes a request.</span></span>
1. <span data-ttu-id="bf8d7-134">Viene restituita la pagina predefinita, in genere `index.html` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-134">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="bf8d7-135">`index.html`avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-135">`index.html` bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="bf8d7-136">il router viene caricato e il componente viene sottoposto a Razor `Main` rendering.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-136">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="bf8d7-137">Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-137">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="bf8d7-138">Tutte le richieste per gli endpoint interni *all'interno dell' Blazor WebAssembly app* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-138">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="bf8d7-139">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-139">The router handles the requests internally.</span></span>

<span data-ttu-id="bf8d7-140">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-140">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="bf8d7-141">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-141">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="bf8d7-142">Poiché i browser effettuano richieste a host basati su Internet per le pagine lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste di risorse non fisicamente presenti sul server nella `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-142">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="bf8d7-143">Quando `index.html` viene restituito, il router dell'app Blazor accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-143">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="bf8d7-144">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file pubblicato dell'app `web.config` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-144">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="bf8d7-145">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-145">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="bf8d7-146">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf8d7-146">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="bf8d7-147">Una *distribuzione ospitata* serve all' Blazor WebAssembly app per i browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-147">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="bf8d7-148">L' Blazor WebAssembly app client viene pubblicata nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-148">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="bf8d7-149">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-149">The two apps are deployed together.</span></span> <span data-ttu-id="bf8d7-150">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-150">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="bf8d7-151">Per una distribuzione ospitata, Visual Studio include il modello di progetto \*\* Blazor WebAssembly app\*\* ( `blazorwasm` modello quando si usa il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) con l' **`Hosted`** opzione selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-151">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="bf8d7-152">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-152">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="bf8d7-153">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-153">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="bf8d7-154">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="bf8d7-154">Standalone deployment</span></span>

<span data-ttu-id="bf8d7-155">Una *distribuzione autonoma* serve l' Blazor WebAssembly app come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-155">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="bf8d7-156">Qualsiasi file server statico è in grado di gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-156">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="bf8d7-157">Le risorse di distribuzione autonome vengono pubblicate nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-157">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="bf8d7-158">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="bf8d7-158">Azure App Service</span></span>

Blazor WebAssembly<span data-ttu-id="bf8d7-159">le app possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-159"> apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="bf8d7-160">La distribuzione di un'app autonoma Blazor WebAssembly nel servizio app Azure per Linux non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-160">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="bf8d7-161">Un'immagine server Linux per ospitare l'app non è disponibile in questo momento.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-161">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="bf8d7-162">Il lavoro è in corso per abilitare questo scenario.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-162">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="bf8d7-163">IIS</span><span class="sxs-lookup"><span data-stu-id="bf8d7-163">IIS</span></span>

<span data-ttu-id="bf8d7-164">IIS è un file server statico idoneo per le Blazor app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-164">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="bf8d7-165">Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-165">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="bf8d7-166">Gli asset pubblicati vengono creati nella `/bin/Release/{TARGET FRAMEWORK}/publish` cartella.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-166">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="bf8d7-167">Ospitare il contenuto della `publish` cartella nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-167">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="bf8d7-168">web.config</span><span class="sxs-lookup"><span data-stu-id="bf8d7-168">web.config</span></span>

<span data-ttu-id="bf8d7-169">Quando Blazor viene pubblicato un progetto, `web.config` viene creato un file con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-169">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="bf8d7-170">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-170">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="bf8d7-171">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-171">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="bf8d7-172">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-172">`.json`: `application/json`</span></span>
  * <span data-ttu-id="bf8d7-173">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-173">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="bf8d7-174">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-174">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="bf8d7-175">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-175">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="bf8d7-176">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-176">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="bf8d7-177">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-177">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="bf8d7-178">Gestire la sottodirectory in cui risiedono gli asset statici dell'app ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-178">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="bf8d7-179">Creare il routing di fallback SPA in modo che le richieste di risorse non file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-179">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="bf8d7-180">Usare un web.config personalizzato</span><span class="sxs-lookup"><span data-stu-id="bf8d7-180">Use a custom web.config</span></span>

<span data-ttu-id="bf8d7-181">Per usare un `web.config` file personalizzato, inserire il `web.config` file personalizzato nella radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-181">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="bf8d7-182">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="bf8d7-182">Install the URL Rewrite Module</span></span>

<span data-ttu-id="bf8d7-183">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-183">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="bf8d7-184">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-184">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="bf8d7-185">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-185">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="bf8d7-186">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-186">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="bf8d7-187">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-187">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="bf8d7-188">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-188">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="bf8d7-189">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-189">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="bf8d7-190">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-190">Copy the installer to the server.</span></span> <span data-ttu-id="bf8d7-191">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-191">Run the installer.</span></span> <span data-ttu-id="bf8d7-192">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-192">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="bf8d7-193">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-193">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="bf8d7-194">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="bf8d7-194">Configure the website</span></span>

<span data-ttu-id="bf8d7-195">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-195">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="bf8d7-196">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-196">The folder contains:</span></span>

* <span data-ttu-id="bf8d7-197">Il `web.config` file utilizzato da IIS per configurare il sito Web, incluse le regole di reindirizzamento richieste e i tipi di contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-197">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="bf8d7-198">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-198">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="bf8d7-199">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="bf8d7-199">Host as an IIS sub-app</span></span>

<span data-ttu-id="bf8d7-200">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-200">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="bf8d7-201">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-201">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="bf8d7-202">Rimuovere il gestore nel Blazor file pubblicato dell'app `web.config` aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-202">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="bf8d7-203">Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :</span><span class="sxs-lookup"><span data-stu-id="bf8d7-203">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="bf8d7-204">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-204">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="bf8d7-205">Impostare il percorso di base dell'app nel file dell'app `index.html` sull'alias IIS usato durante la configurazione della Sub-app in IIS.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-205">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="bf8d7-206">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="bf8d7-206">Brotli and Gzip compression</span></span>

<span data-ttu-id="bf8d7-207">IIS può essere configurato tramite `web.config` per gestire asset Brotli o gzip compressi Blazor .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-207">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="bf8d7-208">Per una configurazione di esempio, vedere [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-208">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="bf8d7-209">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="bf8d7-209">Troubleshooting</span></span>

<span data-ttu-id="bf8d7-210">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-210">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="bf8d7-211">Quando il modulo non è installato, il `web.config` file non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-211">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="bf8d7-212">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-212">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="bf8d7-213">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-213">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="bf8d7-214">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="bf8d7-214">Azure Storage</span></span>

<span data-ttu-id="bf8d7-215">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-215">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="bf8d7-216">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-216">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="bf8d7-217">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-217">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="bf8d7-218">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-218">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="bf8d7-219">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-219">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="bf8d7-220">i componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-220"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="bf8d7-221">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-221">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="bf8d7-222">`index.html`Viene restituito il BLOB e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-222">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="bf8d7-223">Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-223">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="bf8d7-224">Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-224">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="bf8d7-225">Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-225">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="bf8d7-226">In Storage Explorer (portale di Azure) per ogni file:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-226">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="bf8d7-227">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-227">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="bf8d7-228">Impostare **ContentType** e selezionare il pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-228">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="bf8d7-229">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-229">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="bf8d7-230">Nginx</span><span class="sxs-lookup"><span data-stu-id="bf8d7-230">Nginx</span></span>

<span data-ttu-id="bf8d7-231">Il `nginx.conf` file seguente è stato semplificato per illustrare come configurare Nginx per inviare il `index.html` file ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-231">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="bf8d7-232">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-232">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="bf8d7-233">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="bf8d7-233">Nginx in Docker</span></span>

<span data-ttu-id="bf8d7-234">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-234">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="bf8d7-235">Aggiornare Dockerfile per copiare il `nginx.config` file nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-235">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="bf8d7-236">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-236">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="bf8d7-237">Apache</span><span class="sxs-lookup"><span data-stu-id="bf8d7-237">Apache</span></span>

<span data-ttu-id="bf8d7-238">Per distribuire un' Blazor WebAssembly app in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-238">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="bf8d7-239">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-239">Create the Apache configuration file.</span></span> <span data-ttu-id="bf8d7-240">L'esempio seguente è un file di configurazione semplificato ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="bf8d7-240">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="bf8d7-241">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-241">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="bf8d7-242">Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-242">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="bf8d7-243">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-243">Restart the Apache service.</span></span>

<span data-ttu-id="bf8d7-244">Per ulteriori informazioni, vedere [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-244">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="bf8d7-245">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="bf8d7-245">GitHub Pages</span></span>

<span data-ttu-id="bf8d7-246">Per gestire le riscritture URL, aggiungere un `404.html` file con uno script che gestisce il reindirizzamento della richiesta alla `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-246">To handle URL rewrites, add a `404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="bf8d7-247">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-247">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="bf8d7-248">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-248">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="bf8d7-249">Quando si usa un sito del progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il `<base>` tag in `index.html` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-249">When using a project site instead of an organization site, add or update the `<base>` tag in `index.html`.</span></span> <span data-ttu-id="bf8d7-250">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-250">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="bf8d7-251">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="bf8d7-251">Host configuration values</span></span>

<span data-ttu-id="bf8d7-252">le [ Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-252">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="bf8d7-253">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="bf8d7-253">Content root</span></span>

<span data-ttu-id="bf8d7-254">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-254">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="bf8d7-255">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-255">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="bf8d7-256">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-256">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="bf8d7-257">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-257">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="bf8d7-258">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-258">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="bf8d7-259">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-259">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="bf8d7-260">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-260">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="bf8d7-261">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-261">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="bf8d7-262">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="bf8d7-262">Path base</span></span>

<span data-ttu-id="bf8d7-263">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-263">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="bf8d7-264">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-264">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="bf8d7-265">Per altre informazioni, vedere [percorso di base dell'app](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-265">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bf8d7-266">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-266">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="bf8d7-267">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-267">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="bf8d7-268">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-268">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="bf8d7-269">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-269">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="bf8d7-270">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-270">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="bf8d7-271">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-271">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="bf8d7-272">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-272">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="bf8d7-273">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-273">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="bf8d7-274">URL</span><span class="sxs-lookup"><span data-stu-id="bf8d7-274">URLs</span></span>

<span data-ttu-id="bf8d7-275">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-275">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="bf8d7-276">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-276">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="bf8d7-277">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-277">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="bf8d7-278">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-278">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="bf8d7-279">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-279">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="bf8d7-280">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-280">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="bf8d7-281">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-281">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="bf8d7-282">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="bf8d7-282">Configure the Linker</span></span>

Blazor<span data-ttu-id="bf8d7-283">esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-283"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="bf8d7-284">Per altre informazioni, vedere <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-284">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="bf8d7-285">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="bf8d7-285">Custom boot resource loading</span></span>

<span data-ttu-id="bf8d7-286">Un' Blazor WebAssembly app può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-286">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="bf8d7-287">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-287">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="bf8d7-288">Consente agli utenti di caricare risorse statiche, ad esempio dati del fuso orario o `dotnet.wasm` da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-288">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="bf8d7-289">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-289">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="bf8d7-290">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-290">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="bf8d7-291">`loadBootResource`i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-291">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="bf8d7-292">Parametro</span><span class="sxs-lookup"><span data-stu-id="bf8d7-292">Parameter</span></span>    | <span data-ttu-id="bf8d7-293">Descrizione</span><span class="sxs-lookup"><span data-stu-id="bf8d7-293">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="bf8d7-294">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-294">The type of the resource.</span></span> <span data-ttu-id="bf8d7-295">Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="bf8d7-295">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="bf8d7-296">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-296">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="bf8d7-297">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-297">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="bf8d7-298">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-298">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="bf8d7-299">`loadBootResource`restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-299">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="bf8d7-300">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-300">URI string.</span></span> <span data-ttu-id="bf8d7-301">Nell'esempio seguente ( `wwwroot/index.html` ), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="bf8d7-301">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="bf8d7-302">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="bf8d7-302">Timezone data</span></span>

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

* <span data-ttu-id="bf8d7-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-303">`Promise<Response>`.</span></span> <span data-ttu-id="bf8d7-304">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="bf8d7-305">L'esempio seguente ( `wwwroot/index.html` ) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-305">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="bf8d7-306">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="bf8d7-307">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="bf8d7-308">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="bf8d7-309">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="bf8d7-310">I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="bf8d7-311">Modificare l'estensione di file DLL</span><span class="sxs-lookup"><span data-stu-id="bf8d7-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="bf8d7-312">Se è necessario modificare le estensioni dei file pubblicati dell'app `.dll` , seguire le istruzioni riportate in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-312">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="bf8d7-313">Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare `.dll` i file in modo da usare un'estensione di file diversa.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-313">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="bf8d7-314">Specificare come destinazione i `.dll` file nella `wwwroot` directory dell'output pubblicato dell'app (ad esempio, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-314">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="bf8d7-315">Negli esempi seguenti, `.dll` i file vengono rinominati per l'utilizzo dell' `.bin` estensione di file.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-315">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="bf8d7-316">In Windows:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="bf8d7-317">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="bf8d7-318">In Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="bf8d7-319">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="bf8d7-320">Per usare un'estensione di file diversa da `.bin` , sostituire `.bin` nei comandi precedenti.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-320">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="bf8d7-321">Per risolvere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-321">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="bf8d7-322">Rimuovere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-322">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="bf8d7-323">La compressione è disabilitata con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="bf8d7-324">Ricomprimere il `blazor.boot.json` file aggiornato.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-324">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="bf8d7-325">Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="bf8d7-326">Rimuovere o ricomprimere `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="bf8d7-326">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="bf8d7-327">In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="bf8d7-328">Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="bf8d7-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="bf8d7-329">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-329">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="bf8d7-330">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="bf8d7-331">Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="bf8d7-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="bf8d7-332">Per fornire commenti e suggerimenti, vedere [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="bf8d7-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
