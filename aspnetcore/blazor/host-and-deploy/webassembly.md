---
title: Ospitare e distribuire ASP.NET Core Blazor WebAssembly
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine github.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 3436620123618ab32daa44c4a37057aaadb89563
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393691"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="1ca57-103">Ospitare e distribuire ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="1ca57-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="1ca57-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="1ca57-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="1ca57-105">Con il [ Blazor WebAssembly modello di hosting](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="1ca57-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="1ca57-106">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="1ca57-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="1ca57-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="1ca57-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="1ca57-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="1ca57-109">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ca57-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="1ca57-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="1ca57-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="1ca57-111">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="1ca57-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor WebAssembly app come app secondaria IIS.</span><span class="sxs-lookup"><span data-stu-id="1ca57-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="1ca57-113">Compressione</span><span class="sxs-lookup"><span data-stu-id="1ca57-113">Compression</span></span>

<span data-ttu-id="1ca57-114">Quando Blazor WebAssembly viene pubblicata un'app, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="1ca57-115">Vengono utilizzati gli algoritmi di compressione seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="1ca57-116">[Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)</span><span class="sxs-lookup"><span data-stu-id="1ca57-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="1ca57-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="1ca57-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="1ca57-118">Blazor si basa sull'host per gestire i file compressi appropriati.</span><span class="sxs-lookup"><span data-stu-id="1ca57-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="1ca57-119">Quando si usa un ASP.NET Core progetto ospitato, il progetto host è in grado di eseguire la negoziazione del contenuto e di servire i file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="1ca57-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="1ca57-120">Quando si ospita un' Blazor WebAssembly app autonoma, potrebbe essere necessario lavoro aggiuntivo per garantire che vengano serviti file compressi in modo statico:</span><span class="sxs-lookup"><span data-stu-id="1ca57-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="1ca57-121">Per `web.config` la configurazione della compressione IIS, vedere la sezione [IIS: Brotli e la compressione gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="1ca57-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="1ca57-122">Quando si ospitano soluzioni di hosting statiche che non supportano la negoziazione del contenuto di file compressi in modo statico, ad esempio pagine di GitHub, è consigliabile configurare l'app per recuperare e decodificare i file compressi Brotli:</span><span class="sxs-lookup"><span data-stu-id="1ca57-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="1ca57-123">Ottenere il decodificatore JavaScript Brotli dal [repository GitHub Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="1ca57-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="1ca57-124">A partire da settembre 2020, il file decodificatore viene denominato `decode.js` e trovato nella [ `js` cartella](https://github.com/google/brotli/tree/master/js)del repository.</span><span class="sxs-lookup"><span data-stu-id="1ca57-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="1ca57-125">Una regressione è presente nella versione minimizzati dello `decode.js` script ( `decode.min.js` ) nel [repository GitHub Google/brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="1ca57-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="1ca57-126">Minimizzare lo script autonomamente o usare il [pacchetto NPM](https://www.npmjs.com/package/brotli) fino a quando [non viene impostata la finestra di problema. BrotliDecode non è impostato in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) viene risolto.</span><span class="sxs-lookup"><span data-stu-id="1ca57-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="1ca57-127">Il codice di esempio in questa sezione usa la versione **unminified** dello script.</span><span class="sxs-lookup"><span data-stu-id="1ca57-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="1ca57-128">Aggiornare l'app per l'uso del decodificatore.</span><span class="sxs-lookup"><span data-stu-id="1ca57-128">Update the app to use the decoder.</span></span> <span data-ttu-id="1ca57-129">Modificare il markup all'interno del `<body>` tag di chiusura in `wwwroot/index.html` come segue:</span><span class="sxs-lookup"><span data-stu-id="1ca57-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
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
 
<span data-ttu-id="1ca57-130">Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :</span><span class="sxs-lookup"><span data-stu-id="1ca57-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="1ca57-131">La `BlazorEnableCompression` proprietà può essere passata al [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con la sintassi seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="1ca57-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="1ca57-132">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="1ca57-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="1ca57-133">Il routing delle richieste per i componenti della pagina in un' Blazor WebAssembly app non è semplice come il routing delle richieste in un' Blazor Server app ospitata.</span><span class="sxs-lookup"><span data-stu-id="1ca57-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="1ca57-134">Si consideri un' Blazor WebAssembly app con due componenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="1ca57-135">`Main.razor`: Carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="1ca57-136">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="1ca57-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="1ca57-137">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="1ca57-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="1ca57-138">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="1ca57-138">The browser makes a request.</span></span>
1. <span data-ttu-id="1ca57-139">Viene restituita la pagina predefinita, in genere `index.html` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="1ca57-140">`index.html` avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="1ca57-141">Blazoril router viene caricato e il componente viene sottoposto a Razor `Main` rendering.</span><span class="sxs-lookup"><span data-stu-id="1ca57-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="1ca57-142">Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="1ca57-143">Tutte le richieste per gli endpoint interni *all'interno dell' Blazor WebAssembly app* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="1ca57-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="1ca57-144">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="1ca57-144">The router handles the requests internally.</span></span>

<span data-ttu-id="1ca57-145">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="1ca57-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="1ca57-146">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="1ca57-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="1ca57-147">Poiché i browser effettuano richieste a host basati su Internet per le pagine lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste di risorse non fisicamente presenti sul server nella `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="1ca57-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="1ca57-148">Quando `index.html` viene restituito, il router dell'app Blazor accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="1ca57-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="1ca57-149">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file pubblicato dell'app `web.config` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="1ca57-150">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="1ca57-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="1ca57-151">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1ca57-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="1ca57-152">Una *distribuzione ospitata* serve all' Blazor WebAssembly app per i browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="1ca57-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="1ca57-153">L' Blazor WebAssembly app client viene pubblicata nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="1ca57-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="1ca57-154">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="1ca57-154">The two apps are deployed together.</span></span> <span data-ttu-id="1ca57-155">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1ca57-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="1ca57-156">Per una distribuzione ospitata, Visual Studio include il modello di progetto \*\* Blazor WebAssembly app\*\* ( `blazorwasm` modello quando si usa il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) con l' **`Hosted`** opzione selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="1ca57-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="1ca57-157">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="1ca57-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="1ca57-158">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="1ca57-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="1ca57-159">Distribuzione ospitata con più Blazor WebAssembly app</span><span class="sxs-lookup"><span data-stu-id="1ca57-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="1ca57-160">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="1ca57-160">App configuration</span></span>

<span data-ttu-id="1ca57-161">Per configurare una soluzione ospitata Blazor per gestire più Blazor WebAssembly app:</span><span class="sxs-lookup"><span data-stu-id="1ca57-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="1ca57-162">Usare una soluzione ospitata esistente Blazor o creare una nuova soluzione dal Blazor modello di progetto ospitato.</span><span class="sxs-lookup"><span data-stu-id="1ca57-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="1ca57-163">Nel file di progetto dell'app client aggiungere una `<StaticWebAssetBasePath>` proprietà a `<PropertyGroup>` con un valore di `FirstApp` per impostare il percorso di base per gli asset statici del progetto:</span><span class="sxs-lookup"><span data-stu-id="1ca57-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="1ca57-164">Aggiungere una seconda app client alla soluzione:</span><span class="sxs-lookup"><span data-stu-id="1ca57-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="1ca57-165">Aggiungere una cartella denominata `SecondClient` alla cartella della soluzione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="1ca57-166">Creare un' Blazor WebAssembly App denominata `SecondBlazorApp.Client` nella `SecondClient` cartella dal modello di Blazor WebAssembly progetto.</span><span class="sxs-lookup"><span data-stu-id="1ca57-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="1ca57-167">Nel file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="1ca57-167">In the app's project file:</span></span>

    * <span data-ttu-id="1ca57-168">Aggiungere una `<StaticWebAssetBasePath>` proprietà a `<PropertyGroup>` con un valore di `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="1ca57-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="1ca57-169">Aggiungere un riferimento al progetto `Shared` :</span><span class="sxs-lookup"><span data-stu-id="1ca57-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="1ca57-170">Il segnaposto `{SOLUTION NAME}` è il nome della soluzione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="1ca57-171">Nel file di progetto dell'app Server creare un riferimento al progetto per l'app client aggiunta:</span><span class="sxs-lookup"><span data-stu-id="1ca57-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="1ca57-172">Nel file dell'app Server `Properties/launchSettings.json` configurare il `applicationUrl` del profilo gheppio ( `{SOLUTION NAME}.Server` ) per accedere alle app client sulle porte 5001 e 5002:</span><span class="sxs-lookup"><span data-stu-id="1ca57-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="1ca57-173">Nel metodo dell'app Server `Startup.Configure` ( `Startup.cs` ) rimuovere le righe seguenti, che vengono visualizzate dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="1ca57-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="1ca57-174">Aggiungere un middleware che esegue il mapping delle richieste alle app client.</span><span class="sxs-lookup"><span data-stu-id="1ca57-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="1ca57-175">Nell'esempio seguente viene configurato il middleware per l'esecuzione nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="1ca57-176">La porta di richiesta è 5001 per l'app client originale o 5002 per l'app client aggiunta.</span><span class="sxs-lookup"><span data-stu-id="1ca57-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="1ca57-177">L'host della richiesta è `firstapp.com` per l'app client originale o `secondapp.com` per l'app client aggiunta.</span><span class="sxs-lookup"><span data-stu-id="1ca57-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="1ca57-178">L'esempio illustrato in questa sezione richiede una configurazione aggiuntiva per:</span><span class="sxs-lookup"><span data-stu-id="1ca57-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="1ca57-179">Accesso alle app nei domini host di esempio, `firstapp.com` e `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="1ca57-180">Certificati per le app client per abilitare la sicurezza TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="1ca57-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="1ca57-181">La configurazione richiesta esula dall'ambito di questo articolo e dipende dalla modalità di hosting della soluzione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="1ca57-182">Per ulteriori informazioni, vedere gli [articoli host e deploy](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="1ca57-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="1ca57-183">Inserire il codice seguente in cui le righe sono state rimosse in precedenza:</span><span class="sxs-lookup"><span data-stu-id="1ca57-183">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="1ca57-184">Nel controller delle previsioni meteorologiche dell'app Server ( `Controllers/WeatherForecastController.cs` ) sostituire la route esistente ( `[Route("[controller]")]` ) `WeatherForecastController` con le route seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="1ca57-185">Il middleware aggiunto al metodo dell'app Server `Startup.Configure` precedente modifica le richieste in ingresso in `/WeatherForecast` in `/FirstApp/WeatherForecast` o a `/SecondApp/WeatherForecast` seconda della porta (5001/5002) o del dominio ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="1ca57-186">Le route del controller precedenti sono necessarie per restituire i dati meteo dall'app Server alle app client.</span><span class="sxs-lookup"><span data-stu-id="1ca57-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="1ca57-187">Asset statici e librerie di classi</span><span class="sxs-lookup"><span data-stu-id="1ca57-187">Static assets and class libraries</span></span>

<span data-ttu-id="1ca57-188">Usare gli approcci seguenti per gli asset statici:</span><span class="sxs-lookup"><span data-stu-id="1ca57-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="1ca57-189">Quando l'asset si trova nella cartella dell'app client `wwwroot` , fornire i percorsi in genere:</span><span class="sxs-lookup"><span data-stu-id="1ca57-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="1ca57-190">Quando l'asset si trova nella `wwwroot` cartella di una [ Razor libreria di classi (RCL)](xref:blazor/components/class-libraries), fare riferimento all'asset statico nell'app client secondo le indicazioni contenute nell' [articolo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="1ca57-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1ca57-191">Per i componenti forniti a un'app client da una libreria di classi viene fatto riferimento normalmente.</span><span class="sxs-lookup"><span data-stu-id="1ca57-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="1ca57-192">Se i componenti richiedono fogli di stile o file JavaScript, usare uno degli approcci seguenti per ottenere gli asset statici:</span><span class="sxs-lookup"><span data-stu-id="1ca57-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="1ca57-193">Il file dell'app client `wwwroot/index.html` può collegare ( `<link>` ) agli asset statici.</span><span class="sxs-lookup"><span data-stu-id="1ca57-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="1ca57-194">Il componente può usare il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del Framework per ottenere gli asset statici.</span><span class="sxs-lookup"><span data-stu-id="1ca57-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="1ca57-195">Gli approcci precedenti sono illustrati negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="1ca57-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1ca57-196">Per i componenti forniti a un'app client da una libreria di classi viene fatto riferimento normalmente.</span><span class="sxs-lookup"><span data-stu-id="1ca57-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="1ca57-197">Se i componenti richiedono fogli di stile o file JavaScript, il file dell'app client `wwwroot/index.html` deve includere i collegamenti statici corretti.</span><span class="sxs-lookup"><span data-stu-id="1ca57-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="1ca57-198">Questi approcci sono illustrati negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="1ca57-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="1ca57-199">Aggiungere il `Jeep` componente seguente a una delle app client.</span><span class="sxs-lookup"><span data-stu-id="1ca57-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="1ca57-200">Il `Jeep` componente USA:</span><span class="sxs-lookup"><span data-stu-id="1ca57-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="1ca57-201">Un'immagine dalla cartella dell'app client `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="1ca57-202">Un'immagine da una cartella della [ Razor libreria dei componenti](xref:blazor/components/class-libraries) () aggiunta () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="1ca57-203">Il componente di esempio ( `Component1` ) viene creato automaticamente dal modello di progetto RCL quando la `JeepImage` libreria viene aggiunta alla soluzione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="1ca57-204">**Non** pubblicare le immagini dei veicoli pubblicamente, a meno che non si possiedano le immagini.</span><span class="sxs-lookup"><span data-stu-id="1ca57-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="1ca57-205">In caso contrario, si rischia di violare il copyright.</span><span class="sxs-lookup"><span data-stu-id="1ca57-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1ca57-206">È `jeep-yj.png` possibile aggiungere l'immagine della libreria anche al componente della libreria `Component1` ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="1ca57-207">Per fornire la `my-component` classe CSS alla pagina dell'app client, eseguire il collegamento al foglio di stile della libreria usando il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)del Framework:</span><span class="sxs-lookup"><span data-stu-id="1ca57-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="1ca57-208">Un'alternativa all'utilizzo del [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) consiste nel caricare il foglio di stile dal file dell'app client `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="1ca57-209">Questo approccio rende disponibile il foglio di stile per tutti i componenti dell'app client:</span><span class="sxs-lookup"><span data-stu-id="1ca57-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1ca57-210">È `jeep-yj.png` possibile aggiungere l'immagine della libreria anche al componente della libreria `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="1ca57-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="1ca57-211">Il file dell'app client `wwwroot/index.html` richiede il foglio di stile della libreria con il seguente `<link>` tag aggiunto:</span><span class="sxs-lookup"><span data-stu-id="1ca57-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="1ca57-212">Aggiungere la navigazione al `Jeep` componente nel componente dell'app client `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="1ca57-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="1ca57-213">Per ulteriori informazioni su RCL, vedere:</span><span class="sxs-lookup"><span data-stu-id="1ca57-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="1ca57-214">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="1ca57-214">Standalone deployment</span></span>

<span data-ttu-id="1ca57-215">Una *distribuzione autonoma* serve l' Blazor WebAssembly app come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="1ca57-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="1ca57-216">Qualsiasi file server statico è in grado di gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="1ca57-217">Le risorse di distribuzione autonome vengono pubblicate nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella.</span><span class="sxs-lookup"><span data-stu-id="1ca57-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="1ca57-218">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="1ca57-218">Azure App Service</span></span>

<span data-ttu-id="1ca57-219">Blazor WebAssembly le app possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="1ca57-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="1ca57-220">La distribuzione di un'app autonoma Blazor WebAssembly nel servizio app Azure per Linux non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="1ca57-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="1ca57-221">Un'immagine server Linux per ospitare l'app non è disponibile in questo momento.</span><span class="sxs-lookup"><span data-stu-id="1ca57-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="1ca57-222">Il lavoro è in corso per abilitare questo scenario.</span><span class="sxs-lookup"><span data-stu-id="1ca57-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="1ca57-223">IIS</span><span class="sxs-lookup"><span data-stu-id="1ca57-223">IIS</span></span>

<span data-ttu-id="1ca57-224">IIS è un file server statico idoneo per le Blazor app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="1ca57-225">Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="1ca57-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="1ca57-226">Gli asset pubblicati vengono creati nella `/bin/Release/{TARGET FRAMEWORK}/publish` cartella.</span><span class="sxs-lookup"><span data-stu-id="1ca57-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="1ca57-227">Ospitare il contenuto della `publish` cartella nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="1ca57-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="1ca57-228">web.config</span><span class="sxs-lookup"><span data-stu-id="1ca57-228">web.config</span></span>

<span data-ttu-id="1ca57-229">Quando Blazor viene pubblicato un progetto, `web.config` viene creato un file con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="1ca57-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="1ca57-230">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="1ca57-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="1ca57-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="1ca57-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="1ca57-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="1ca57-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="1ca57-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="1ca57-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1ca57-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="1ca57-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1ca57-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="1ca57-236">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="1ca57-237">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="1ca57-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="1ca57-238">Gestire la sottodirectory in cui risiedono gli asset statici dell'app ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="1ca57-239">Creare il routing di fallback SPA in modo che le richieste di risorse non file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="1ca57-240">Usare un web.config personalizzato</span><span class="sxs-lookup"><span data-stu-id="1ca57-240">Use a custom web.config</span></span>

<span data-ttu-id="1ca57-241">Per usare un `web.config` file personalizzato, inserire il `web.config` file personalizzato nella radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="1ca57-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="1ca57-242">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="1ca57-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="1ca57-243">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="1ca57-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="1ca57-244">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="1ca57-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="1ca57-245">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="1ca57-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="1ca57-246">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="1ca57-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="1ca57-247">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="1ca57-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="1ca57-248">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="1ca57-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="1ca57-249">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="1ca57-250">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="1ca57-250">Copy the installer to the server.</span></span> <span data-ttu-id="1ca57-251">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-251">Run the installer.</span></span> <span data-ttu-id="1ca57-252">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="1ca57-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="1ca57-253">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="1ca57-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="1ca57-254">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="1ca57-254">Configure the website</span></span>

<span data-ttu-id="1ca57-255">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="1ca57-256">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="1ca57-256">The folder contains:</span></span>

* <span data-ttu-id="1ca57-257">Il `web.config` file utilizzato da IIS per configurare il sito Web, incluse le regole di reindirizzamento richieste e i tipi di contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="1ca57-258">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="1ca57-259">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="1ca57-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="1ca57-260">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="1ca57-261">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="1ca57-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="1ca57-262">Rimuovere il gestore nel Blazor file pubblicato dell'app `web.config` aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="1ca57-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="1ca57-263">Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :</span><span class="sxs-lookup"><span data-stu-id="1ca57-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="1ca57-264">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="1ca57-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="1ca57-265">Impostare il percorso di base dell'app nel file dell'app `index.html` sull'alias IIS usato durante la configurazione della Sub-app in IIS.</span><span class="sxs-lookup"><span data-stu-id="1ca57-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="1ca57-266">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="1ca57-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="1ca57-267">IIS può essere configurato tramite `web.config` per gestire asset Brotli o gzip compressi Blazor .</span><span class="sxs-lookup"><span data-stu-id="1ca57-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="1ca57-268">Per una configurazione di esempio, vedere [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="1ca57-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="1ca57-269">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="1ca57-269">Troubleshooting</span></span>

<span data-ttu-id="1ca57-270">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="1ca57-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="1ca57-271">Quando il modulo non è installato, il `web.config` file non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="1ca57-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="1ca57-272">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="1ca57-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="1ca57-273">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="1ca57-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="1ca57-274">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="1ca57-274">Azure Storage</span></span>

<span data-ttu-id="1ca57-275">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor .</span><span class="sxs-lookup"><span data-stu-id="1ca57-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="1ca57-276">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1ca57-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="1ca57-277">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="1ca57-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="1ca57-278">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="1ca57-279">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="1ca57-280">Razor i componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="1ca57-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="1ca57-281">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="1ca57-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="1ca57-282">`index.html`Viene restituito il BLOB e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="1ca57-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="1ca57-283">Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="1ca57-284">Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="1ca57-285">Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="1ca57-286">In Storage Explorer (portale di Azure) per ogni file:</span><span class="sxs-lookup"><span data-stu-id="1ca57-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="1ca57-287">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="1ca57-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="1ca57-288">Impostare **ContentType** e selezionare il pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="1ca57-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="1ca57-289">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="1ca57-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="1ca57-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="1ca57-290">Nginx</span></span>

<span data-ttu-id="1ca57-291">Il `nginx.conf` file seguente è stato semplificato per illustrare come configurare Nginx per inviare il `index.html` file ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="1ca57-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="1ca57-292">Quando si imposta il [limite di velocità](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) di espansione nginx con, le [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) Blazor WebAssembly app possono richiedere un `burst` valore di parametro di grandi dimensioni per contenere il numero relativamente elevato di richieste effettuate da un'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="1ca57-293">Inizialmente, impostare il valore su almeno 60:</span><span class="sxs-lookup"><span data-stu-id="1ca57-293">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="1ca57-294">Aumentare il valore se browser Developer Tools o uno strumento di traffico di rete indica che le richieste ricevono un codice di stato *503-servizio non disponibile* .</span><span class="sxs-lookup"><span data-stu-id="1ca57-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="1ca57-295">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="1ca57-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="1ca57-296">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="1ca57-296">Nginx in Docker</span></span>

<span data-ttu-id="1ca57-297">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="1ca57-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="1ca57-298">Aggiornare Dockerfile per copiare il `nginx.config` file nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="1ca57-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="1ca57-299">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="1ca57-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="1ca57-300">Apache</span><span class="sxs-lookup"><span data-stu-id="1ca57-300">Apache</span></span>

<span data-ttu-id="1ca57-301">Per distribuire un' Blazor WebAssembly app in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="1ca57-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="1ca57-302">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="1ca57-302">Create the Apache configuration file.</span></span> <span data-ttu-id="1ca57-303">L'esempio seguente è un file di configurazione semplificato ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="1ca57-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="1ca57-304">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="1ca57-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="1ca57-305">Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="1ca57-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="1ca57-306">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="1ca57-306">Restart the Apache service.</span></span>

<span data-ttu-id="1ca57-307">Per altre informazioni, vedere [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="1ca57-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="1ca57-308">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="1ca57-308">GitHub Pages</span></span>

<span data-ttu-id="1ca57-309">Per gestire le riscritture URL, aggiungere un `wwwroot/404.html` file con uno script che gestisce il reindirizzamento della richiesta alla `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="1ca57-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="1ca57-310">Per un esempio, vedere il [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="1ca57-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="1ca57-311">[Sito Live](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="1ca57-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="1ca57-312">Quando si usa un sito del progetto anziché un sito dell'organizzazione, aggiornare il `<base>` tag in `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="1ca57-313">Impostare il `href` valore dell'attributo sul nome del repository GitHub con una barra finale (ad esempio, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="1ca57-314">Nel [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)la base `href` viene aggiornata in fase di pubblicazione dal [ `.github/workflows/main.yml` file di configurazione](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="1ca57-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="1ca57-315">Il [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) non è di proprietà, è stato mantenuto o supportato da .NET Foundation o Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1ca57-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1ca57-316">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="1ca57-316">Host configuration values</span></span>

<span data-ttu-id="1ca57-317">le [ Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="1ca57-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="1ca57-318">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="1ca57-318">Content root</span></span>

<span data-ttu-id="1ca57-319">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="1ca57-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="1ca57-320">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="1ca57-321">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1ca57-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1ca57-322">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="1ca57-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="1ca57-323">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="1ca57-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1ca57-324">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="1ca57-325">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="1ca57-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1ca57-326">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="1ca57-327">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="1ca57-327">Path base</span></span>

<span data-ttu-id="1ca57-328">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="1ca57-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="1ca57-329">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="1ca57-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="1ca57-330">Per altre informazioni, vedere [percorso di base dell'app](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="1ca57-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1ca57-331">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="1ca57-332">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="1ca57-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="1ca57-333">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1ca57-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1ca57-334">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="1ca57-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="1ca57-335">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="1ca57-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1ca57-336">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="1ca57-337">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="1ca57-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1ca57-338">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="1ca57-339">URL</span><span class="sxs-lookup"><span data-stu-id="1ca57-339">URLs</span></span>

<span data-ttu-id="1ca57-340">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="1ca57-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="1ca57-341">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="1ca57-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1ca57-342">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="1ca57-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="1ca57-343">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="1ca57-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="1ca57-344">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="1ca57-345">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="1ca57-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1ca57-346">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="1ca57-347">Configurare il trimmer</span><span class="sxs-lookup"><span data-stu-id="1ca57-347">Configure the Trimmer</span></span>

<span data-ttu-id="1ca57-348">Blazor esegue il trimming del linguaggio intermedio (IL) in ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="1ca57-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="1ca57-349">Per altre informazioni, vedere <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="1ca57-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="1ca57-350">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="1ca57-350">Configure the Linker</span></span>

<span data-ttu-id="1ca57-351">Blazor esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="1ca57-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="1ca57-352">Per altre informazioni, vedere <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="1ca57-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="1ca57-353">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="1ca57-353">Custom boot resource loading</span></span>

<span data-ttu-id="1ca57-354">Un' Blazor WebAssembly app può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="1ca57-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="1ca57-355">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="1ca57-356">Consente agli utenti di caricare risorse statiche, ad esempio dati del fuso orario o `dotnet.wasm` da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="1ca57-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="1ca57-357">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="1ca57-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="1ca57-358">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="1ca57-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="1ca57-359">`loadBootResource` i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="1ca57-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="1ca57-360">Parametro</span><span class="sxs-lookup"><span data-stu-id="1ca57-360">Parameter</span></span>    | <span data-ttu-id="1ca57-361">Descrizione</span><span class="sxs-lookup"><span data-stu-id="1ca57-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="1ca57-362">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="1ca57-362">The type of the resource.</span></span> <span data-ttu-id="1ca57-363">Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="1ca57-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="1ca57-364">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="1ca57-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="1ca57-365">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="1ca57-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="1ca57-366">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="1ca57-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="1ca57-367">`loadBootResource` restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="1ca57-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="1ca57-368">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="1ca57-368">URI string.</span></span> <span data-ttu-id="1ca57-369">Nell'esempio seguente ( `wwwroot/index.html` ), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="1ca57-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="1ca57-370">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="1ca57-370">Timezone data</span></span>

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

* <span data-ttu-id="1ca57-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="1ca57-371">`Promise<Response>`.</span></span> <span data-ttu-id="1ca57-372">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="1ca57-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="1ca57-373">L'esempio seguente ( `wwwroot/index.html` ) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="1ca57-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="1ca57-374">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="1ca57-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="1ca57-375">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="1ca57-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="1ca57-376">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="1ca57-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="1ca57-377">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="1ca57-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="1ca57-378">I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="1ca57-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="1ca57-379">Modificare l'estensione di file DLL</span><span class="sxs-lookup"><span data-stu-id="1ca57-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="1ca57-380">Se è necessario modificare le estensioni dei file pubblicati dell'app `.dll` , seguire le istruzioni riportate in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="1ca57-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="1ca57-381">Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare `.dll` i file in modo da usare un'estensione di file diversa.</span><span class="sxs-lookup"><span data-stu-id="1ca57-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="1ca57-382">Specificare come destinazione i `.dll` file nella `wwwroot` directory dell'output pubblicato dell'app (ad esempio, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="1ca57-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="1ca57-383">Negli esempi seguenti, `.dll` i file vengono rinominati per l'utilizzo dell' `.bin` estensione di file.</span><span class="sxs-lookup"><span data-stu-id="1ca57-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="1ca57-384">In Windows:</span><span class="sxs-lookup"><span data-stu-id="1ca57-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="1ca57-385">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="1ca57-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="1ca57-386">In Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="1ca57-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="1ca57-387">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="1ca57-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="1ca57-388">Per usare un'estensione di file diversa da `.bin` , sostituire `.bin` nei comandi precedenti.</span><span class="sxs-lookup"><span data-stu-id="1ca57-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="1ca57-389">Per risolvere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="1ca57-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="1ca57-390">Rimuovere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="1ca57-391">La compressione è disabilitata con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="1ca57-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="1ca57-392">Ricomprimere il `blazor.boot.json` file aggiornato.</span><span class="sxs-lookup"><span data-stu-id="1ca57-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="1ca57-393">Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso.</span><span class="sxs-lookup"><span data-stu-id="1ca57-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="1ca57-394">Rimuovere o ricomprimere `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="1ca57-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="1ca57-395">In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.</span><span class="sxs-lookup"><span data-stu-id="1ca57-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="1ca57-396">Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="1ca57-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="1ca57-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="1ca57-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="1ca57-398">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="1ca57-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="1ca57-399">Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="1ca57-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="1ca57-400">Quando si rinominano e si caricano in modalità lazy gli stessi assembly, vedere le istruzioni in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="1ca57-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>
