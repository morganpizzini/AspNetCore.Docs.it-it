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
ms.openlocfilehash: 6b4c3d55d77af104c969cac0fcbf642f35c7dd7f
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865261"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="29435-103">Ospitare e distribuire ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="29435-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="29435-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="29435-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="29435-105">Con il [ Blazor WebAssembly modello di hosting](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="29435-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="29435-106">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="29435-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="29435-107">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="29435-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="29435-108">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="29435-109">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29435-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="29435-110">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="29435-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="29435-111">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="29435-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="29435-112">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor WebAssembly app come app secondaria IIS.</span><span class="sxs-lookup"><span data-stu-id="29435-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="29435-113">Compressione</span><span class="sxs-lookup"><span data-stu-id="29435-113">Compression</span></span>

<span data-ttu-id="29435-114">Quando Blazor WebAssembly viene pubblicata un'app, l'output viene compresso in modo statico durante la pubblicazione per ridurre le dimensioni dell'app e rimuovere l'overhead per la compressione in fase di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="29435-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="29435-115">Vengono utilizzati gli algoritmi di compressione seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="29435-116">[Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)</span><span class="sxs-lookup"><span data-stu-id="29435-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="29435-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="29435-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="29435-118">Blazor si basa sull'host per gestire i file compressi appropriati.</span><span class="sxs-lookup"><span data-stu-id="29435-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="29435-119">Quando si usa un ASP.NET Core progetto ospitato, il progetto host è in grado di eseguire la negoziazione del contenuto e di servire i file compressi in modo statico.</span><span class="sxs-lookup"><span data-stu-id="29435-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="29435-120">Quando si ospita un' Blazor WebAssembly app autonoma, potrebbe essere necessario lavoro aggiuntivo per garantire che vengano serviti file compressi in modo statico:</span><span class="sxs-lookup"><span data-stu-id="29435-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="29435-121">Per `web.config` la configurazione della compressione IIS, vedere la sezione [IIS: Brotli e la compressione gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="29435-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="29435-122">Quando si ospitano soluzioni di hosting statiche che non supportano la negoziazione del contenuto di file compressi in modo statico, ad esempio pagine di GitHub, è consigliabile configurare l'app per recuperare e decodificare i file compressi Brotli:</span><span class="sxs-lookup"><span data-stu-id="29435-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="29435-123">Ottenere il decodificatore JavaScript Brotli dal [repository GitHub Google/Brotli](https://github.com/google/brotli).</span><span class="sxs-lookup"><span data-stu-id="29435-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="29435-124">Al 2020 luglio il file decodificatore viene denominato `decode.min.js` e trovato nella [ `js` cartella](https://github.com/google/brotli/tree/master/js)del repository.</span><span class="sxs-lookup"><span data-stu-id="29435-124">As of July 2020, the decoder file is named `decode.min.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  * <span data-ttu-id="29435-125">Aggiornare l'app per l'uso del decodificatore.</span><span class="sxs-lookup"><span data-stu-id="29435-125">Update the app to use the decoder.</span></span> <span data-ttu-id="29435-126">Modificare il markup all'interno del `<body>` tag di chiusura in `wwwroot/index.html` come segue:</span><span class="sxs-lookup"><span data-stu-id="29435-126">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.min.js"></script>
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
 
<span data-ttu-id="29435-127">Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :</span><span class="sxs-lookup"><span data-stu-id="29435-127">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="29435-128">La `BlazorEnableCompression` proprietà può essere passata al [`dotnet publish`](/dotnet/core/tools/dotnet-publish) comando con la sintassi seguente in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="29435-128">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="29435-129">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="29435-129">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="29435-130">Il routing delle richieste per i componenti della pagina in un' Blazor WebAssembly app non è semplice come il routing delle richieste in un' Blazor Server app ospitata.</span><span class="sxs-lookup"><span data-stu-id="29435-130">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="29435-131">Si consideri un' Blazor WebAssembly app con due componenti:</span><span class="sxs-lookup"><span data-stu-id="29435-131">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="29435-132">`Main.razor`: Carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="29435-132">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="29435-133">`About.razor`: `About` componente.</span><span class="sxs-lookup"><span data-stu-id="29435-133">`About.razor`: `About` component.</span></span>

<span data-ttu-id="29435-134">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="29435-134">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="29435-135">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="29435-135">The browser makes a request.</span></span>
1. <span data-ttu-id="29435-136">Viene restituita la pagina predefinita, in genere `index.html` .</span><span class="sxs-lookup"><span data-stu-id="29435-136">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="29435-137">`index.html` avvia l'app.</span><span class="sxs-lookup"><span data-stu-id="29435-137">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="29435-138">Blazoril router viene caricato e il componente viene sottoposto a Razor `Main` rendering.</span><span class="sxs-lookup"><span data-stu-id="29435-138">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="29435-139">Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` .</span><span class="sxs-lookup"><span data-stu-id="29435-139">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="29435-140">Tutte le richieste per gli endpoint interni *all'interno dell' Blazor WebAssembly app* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="29435-140">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="29435-141">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="29435-141">The router handles the requests internally.</span></span>

<span data-ttu-id="29435-142">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="29435-142">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="29435-143">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="29435-143">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="29435-144">Poiché i browser effettuano richieste a host basati su Internet per le pagine lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste di risorse non fisicamente presenti sul server nella `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="29435-144">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="29435-145">Quando `index.html` viene restituito, il router dell'app Blazor accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="29435-145">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="29435-146">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file pubblicato dell'app `web.config` .</span><span class="sxs-lookup"><span data-stu-id="29435-146">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="29435-147">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="29435-147">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="29435-148">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29435-148">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="29435-149">Una *distribuzione ospitata* serve all' Blazor WebAssembly app per i browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="29435-149">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="29435-150">L' Blazor WebAssembly app client viene pubblicata nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="29435-150">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="29435-151">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="29435-151">The two apps are deployed together.</span></span> <span data-ttu-id="29435-152">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="29435-152">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="29435-153">Per una distribuzione ospitata, Visual Studio include il modello di progetto \*\* Blazor WebAssembly app\*\* ( `blazorwasm` modello quando si usa il [`dotnet new`](/dotnet/core/tools/dotnet-new) comando) con l' **`Hosted`** opzione selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="29435-153">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="29435-154">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="29435-154">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="29435-155">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="29435-155">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="29435-156">Distribuzione ospitata con più Blazor WebAssembly app</span><span class="sxs-lookup"><span data-stu-id="29435-156">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="29435-157">Configurazione dell'app</span><span class="sxs-lookup"><span data-stu-id="29435-157">App configuration</span></span>

<span data-ttu-id="29435-158">Per configurare una soluzione ospitata Blazor per gestire più Blazor WebAssembly app:</span><span class="sxs-lookup"><span data-stu-id="29435-158">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="29435-159">Usare una soluzione ospitata esistente Blazor o creare una nuova soluzione dal Blazor modello di progetto ospitato.</span><span class="sxs-lookup"><span data-stu-id="29435-159">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="29435-160">Nel file di progetto dell'app client aggiungere una `<StaticWebAssetBasePath>` proprietà a `<PropertyGroup>` con un valore di `FirstApp` per impostare il percorso di base per gli asset statici del progetto:</span><span class="sxs-lookup"><span data-stu-id="29435-160">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="29435-161">Aggiungere una seconda app client alla soluzione:</span><span class="sxs-lookup"><span data-stu-id="29435-161">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="29435-162">Aggiungere una cartella denominata `SecondClient` alla cartella della soluzione.</span><span class="sxs-lookup"><span data-stu-id="29435-162">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="29435-163">Creare un' Blazor WebAssembly App denominata `SecondBlazorApp.Client` nella `SecondClient` cartella dal modello di Blazor WebAssembly progetto.</span><span class="sxs-lookup"><span data-stu-id="29435-163">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="29435-164">Nel file di progetto dell'app:</span><span class="sxs-lookup"><span data-stu-id="29435-164">In the app's project file:</span></span>

    * <span data-ttu-id="29435-165">Aggiungere una `<StaticWebAssetBasePath>` proprietà a `<PropertyGroup>` con un valore di `SecondApp` :</span><span class="sxs-lookup"><span data-stu-id="29435-165">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="29435-166">Aggiungere un riferimento al progetto `Shared` :</span><span class="sxs-lookup"><span data-stu-id="29435-166">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="29435-167">Il segnaposto `{SOLUTION NAME}` è il nome della soluzione.</span><span class="sxs-lookup"><span data-stu-id="29435-167">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="29435-168">Nel file di progetto dell'app Server creare un riferimento al progetto per l'app client aggiunta:</span><span class="sxs-lookup"><span data-stu-id="29435-168">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="29435-169">Nel file dell'app Server `Properties/launchSettings.json` configurare il `applicationUrl` del profilo gheppio ( `{SOLUTION NAME}.Server` ) per accedere alle app client sulle porte 5001 e 5002:</span><span class="sxs-lookup"><span data-stu-id="29435-169">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="29435-170">Nel metodo dell'app Server `Startup.Configure` ( `Startup.cs` ) rimuovere le righe seguenti, che vengono visualizzate dopo la chiamata a <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :</span><span class="sxs-lookup"><span data-stu-id="29435-170">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="29435-171">Aggiungere un middleware che esegue il mapping delle richieste alle app client.</span><span class="sxs-lookup"><span data-stu-id="29435-171">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="29435-172">Nell'esempio seguente viene configurato il middleware per l'esecuzione nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-172">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="29435-173">La porta di richiesta è 5001 per l'app client originale o 5002 per l'app client aggiunta.</span><span class="sxs-lookup"><span data-stu-id="29435-173">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="29435-174">L'host della richiesta è `firstapp.com` per l'app client originale o `secondapp.com` per l'app client aggiunta.</span><span class="sxs-lookup"><span data-stu-id="29435-174">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="29435-175">L'esempio illustrato in questa sezione richiede una configurazione aggiuntiva per:</span><span class="sxs-lookup"><span data-stu-id="29435-175">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="29435-176">Accesso alle app nei domini host di esempio, `firstapp.com` e `secondapp.com` .</span><span class="sxs-lookup"><span data-stu-id="29435-176">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="29435-177">Certificati per le app client per abilitare la sicurezza TLS (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="29435-177">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="29435-178">La configurazione richiesta esula dall'ambito di questo articolo e dipende dalla modalità di hosting della soluzione.</span><span class="sxs-lookup"><span data-stu-id="29435-178">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="29435-179">Per ulteriori informazioni, vedere gli [articoli host e deploy](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="29435-179">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="29435-180">Inserire il codice seguente in cui le righe sono state rimosse in precedenza:</span><span class="sxs-lookup"><span data-stu-id="29435-180">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="29435-181">Nel controller delle previsioni meteorologiche dell'app Server ( `Controllers/WeatherForecastController.cs` ) sostituire la route esistente ( `[Route("[controller]")]` ) `WeatherForecastController` con le route seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-181">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="29435-182">Il middleware aggiunto al metodo dell'app Server `Startup.Configure` precedente modifica le richieste in ingresso in `/WeatherForecast` in `/FirstApp/WeatherForecast` o a `/SecondApp/WeatherForecast` seconda della porta (5001/5002) o del dominio ( `firstapp.com` / `secondapp.com` ).</span><span class="sxs-lookup"><span data-stu-id="29435-182">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="29435-183">Le route del controller precedenti sono necessarie per restituire i dati meteo dall'app Server alle app client.</span><span class="sxs-lookup"><span data-stu-id="29435-183">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="29435-184">Asset statici e librerie di classi</span><span class="sxs-lookup"><span data-stu-id="29435-184">Static assets and class libraries</span></span>

<span data-ttu-id="29435-185">Usare gli approcci seguenti per gli asset statici:</span><span class="sxs-lookup"><span data-stu-id="29435-185">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="29435-186">Quando l'asset si trova nella cartella dell'app client `wwwroot` , fornire i percorsi in genere:</span><span class="sxs-lookup"><span data-stu-id="29435-186">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="29435-187">Quando l'asset si trova nella `wwwroot` cartella di una [ Razor libreria di classi (RCL)](xref:blazor/components/class-libraries), fare riferimento all'asset statico nell'app client secondo le indicazioni contenute nell' [articolo RCL](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span><span class="sxs-lookup"><span data-stu-id="29435-187">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29435-188">Per i componenti forniti a un'app client da una libreria di classi viene fatto riferimento normalmente.</span><span class="sxs-lookup"><span data-stu-id="29435-188">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="29435-189">Se i componenti richiedono fogli di stile o file JavaScript, usare uno degli approcci seguenti per ottenere gli asset statici:</span><span class="sxs-lookup"><span data-stu-id="29435-189">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="29435-190">Il file dell'app client `wwwroot/index.html` può collegare ( `<link>` ) agli asset statici.</span><span class="sxs-lookup"><span data-stu-id="29435-190">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="29435-191">Il componente può usare il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) del Framework per ottenere gli asset statici.</span><span class="sxs-lookup"><span data-stu-id="29435-191">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="29435-192">Gli approcci precedenti sono illustrati negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="29435-192">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="29435-193">Per i componenti forniti a un'app client da una libreria di classi viene fatto riferimento normalmente.</span><span class="sxs-lookup"><span data-stu-id="29435-193">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="29435-194">Se i componenti richiedono fogli di stile o file JavaScript, il file dell'app client `wwwroot/index.html` deve includere i collegamenti statici corretti.</span><span class="sxs-lookup"><span data-stu-id="29435-194">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="29435-195">Questi approcci sono illustrati negli esempi seguenti.</span><span class="sxs-lookup"><span data-stu-id="29435-195">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="29435-196">Aggiungere il `Jeep` componente seguente a una delle app client.</span><span class="sxs-lookup"><span data-stu-id="29435-196">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="29435-197">Il `Jeep` componente USA:</span><span class="sxs-lookup"><span data-stu-id="29435-197">The `Jeep` component uses:</span></span>

* <span data-ttu-id="29435-198">Un'immagine dalla cartella dell'app client `wwwroot` ( `jeep-cj.png` ).</span><span class="sxs-lookup"><span data-stu-id="29435-198">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="29435-199">Un'immagine da una cartella della [ Razor libreria dei componenti](xref:blazor/components/class-libraries) () aggiunta () `JeepImage` `wwwroot` `jeep-yj.png` .</span><span class="sxs-lookup"><span data-stu-id="29435-199">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="29435-200">Il componente di esempio ( `Component1` ) viene creato automaticamente dal modello di progetto RCL quando la `JeepImage` libreria viene aggiunta alla soluzione.</span><span class="sxs-lookup"><span data-stu-id="29435-200">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="29435-201">**Non** pubblicare le immagini dei veicoli pubblicamente, a meno che non si possiedano le immagini.</span><span class="sxs-lookup"><span data-stu-id="29435-201">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="29435-202">In caso contrario, si rischia di violare il copyright.</span><span class="sxs-lookup"><span data-stu-id="29435-202">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="29435-203">È `jeep-yj.png` possibile aggiungere l'immagine della libreria anche al componente della libreria `Component1` ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="29435-203">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="29435-204">Per fornire la `my-component` classe CSS alla pagina dell'app client, eseguire il collegamento al foglio di stile della libreria usando il [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)del Framework:</span><span class="sxs-lookup"><span data-stu-id="29435-204">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="29435-205">Un'alternativa all'utilizzo del [ `Link` componente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) consiste nel caricare il foglio di stile dal file dell'app client `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="29435-205">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="29435-206">Questo approccio rende disponibile il foglio di stile per tutti i componenti dell'app client:</span><span class="sxs-lookup"><span data-stu-id="29435-206">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="29435-207">È `jeep-yj.png` possibile aggiungere l'immagine della libreria anche al componente della libreria `Component1` ( `Component1.razor` ):</span><span class="sxs-lookup"><span data-stu-id="29435-207">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="29435-208">Il file dell'app client `wwwroot/index.html` richiede il foglio di stile della libreria con il seguente `<link>` tag aggiunto:</span><span class="sxs-lookup"><span data-stu-id="29435-208">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="29435-209">Aggiungere la navigazione al `Jeep` componente nel componente dell'app client `NavMenu` ( `Shared/NavMenu.razor` ):</span><span class="sxs-lookup"><span data-stu-id="29435-209">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="29435-210">Per ulteriori informazioni su RCL, vedere:</span><span class="sxs-lookup"><span data-stu-id="29435-210">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="29435-211">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="29435-211">Standalone deployment</span></span>

<span data-ttu-id="29435-212">Una *distribuzione autonoma* serve l' Blazor WebAssembly app come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="29435-212">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="29435-213">Qualsiasi file server statico è in grado di gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="29435-213">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="29435-214">Le risorse di distribuzione autonome vengono pubblicate nella `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` cartella.</span><span class="sxs-lookup"><span data-stu-id="29435-214">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="29435-215">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="29435-215">Azure App Service</span></span>

<span data-ttu-id="29435-216">Blazor WebAssembly le app possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="29435-216">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="29435-217">La distribuzione di un'app autonoma Blazor WebAssembly nel servizio app Azure per Linux non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="29435-217">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="29435-218">Un'immagine server Linux per ospitare l'app non è disponibile in questo momento.</span><span class="sxs-lookup"><span data-stu-id="29435-218">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="29435-219">Il lavoro è in corso per abilitare questo scenario.</span><span class="sxs-lookup"><span data-stu-id="29435-219">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="29435-220">IIS</span><span class="sxs-lookup"><span data-stu-id="29435-220">IIS</span></span>

<span data-ttu-id="29435-221">IIS è un file server statico idoneo per le Blazor app.</span><span class="sxs-lookup"><span data-stu-id="29435-221">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="29435-222">Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="29435-222">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="29435-223">Gli asset pubblicati vengono creati nella `/bin/Release/{TARGET FRAMEWORK}/publish` cartella.</span><span class="sxs-lookup"><span data-stu-id="29435-223">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="29435-224">Ospitare il contenuto della `publish` cartella nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="29435-224">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="29435-225">web.config</span><span class="sxs-lookup"><span data-stu-id="29435-225">web.config</span></span>

<span data-ttu-id="29435-226">Quando Blazor viene pubblicato un progetto, `web.config` viene creato un file con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="29435-226">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="29435-227">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-227">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="29435-228">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="29435-228">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="29435-229">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="29435-229">`.json`: `application/json`</span></span>
  * <span data-ttu-id="29435-230">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="29435-230">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="29435-231">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="29435-231">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="29435-232">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="29435-232">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="29435-233">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-233">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="29435-234">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="29435-234">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="29435-235">Gestire la sottodirectory in cui risiedono gli asset statici dell'app ( `wwwroot/{PATH REQUESTED}` ).</span><span class="sxs-lookup"><span data-stu-id="29435-235">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="29435-236">Creare il routing di fallback SPA in modo che le richieste di risorse non file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici ( `wwwroot/index.html` ).</span><span class="sxs-lookup"><span data-stu-id="29435-236">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="29435-237">Usare un web.config personalizzato</span><span class="sxs-lookup"><span data-stu-id="29435-237">Use a custom web.config</span></span>

<span data-ttu-id="29435-238">Per usare un `web.config` file personalizzato, inserire il `web.config` file personalizzato nella radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="29435-238">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="29435-239">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="29435-239">Install the URL Rewrite Module</span></span>

<span data-ttu-id="29435-240">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="29435-240">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="29435-241">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="29435-241">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="29435-242">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="29435-242">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="29435-243">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="29435-243">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="29435-244">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="29435-244">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="29435-245">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="29435-245">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="29435-246">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="29435-246">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="29435-247">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="29435-247">Copy the installer to the server.</span></span> <span data-ttu-id="29435-248">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="29435-248">Run the installer.</span></span> <span data-ttu-id="29435-249">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="29435-249">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="29435-250">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="29435-250">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="29435-251">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="29435-251">Configure the website</span></span>

<span data-ttu-id="29435-252">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="29435-252">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="29435-253">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="29435-253">The folder contains:</span></span>

* <span data-ttu-id="29435-254">Il `web.config` file utilizzato da IIS per configurare il sito Web, incluse le regole di reindirizzamento richieste e i tipi di contenuto del file.</span><span class="sxs-lookup"><span data-stu-id="29435-254">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="29435-255">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="29435-255">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="29435-256">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="29435-256">Host as an IIS sub-app</span></span>

<span data-ttu-id="29435-257">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-257">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="29435-258">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="29435-258">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="29435-259">Rimuovere il gestore nel Blazor file pubblicato dell'app `web.config` aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="29435-259">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="29435-260">Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :</span><span class="sxs-lookup"><span data-stu-id="29435-260">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="29435-261">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="29435-261">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="29435-262">Impostare il percorso di base dell'app nel file dell'app `index.html` sull'alias IIS usato durante la configurazione della Sub-app in IIS.</span><span class="sxs-lookup"><span data-stu-id="29435-262">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="29435-263">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="29435-263">Brotli and Gzip compression</span></span>

<span data-ttu-id="29435-264">IIS può essere configurato tramite `web.config` per gestire asset Brotli o gzip compressi Blazor .</span><span class="sxs-lookup"><span data-stu-id="29435-264">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="29435-265">Per una configurazione di esempio, vedere [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) .</span><span class="sxs-lookup"><span data-stu-id="29435-265">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="29435-266">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="29435-266">Troubleshooting</span></span>

<span data-ttu-id="29435-267">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="29435-267">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="29435-268">Quando il modulo non è installato, il `web.config` file non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="29435-268">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="29435-269">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="29435-269">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="29435-270">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="29435-270">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="29435-271">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="29435-271">Azure Storage</span></span>

<span data-ttu-id="29435-272">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor .</span><span class="sxs-lookup"><span data-stu-id="29435-272">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="29435-273">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="29435-273">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="29435-274">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="29435-274">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="29435-275">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="29435-275">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="29435-276">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="29435-276">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="29435-277">Razor i componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="29435-277">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="29435-278">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="29435-278">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="29435-279">`index.html`Viene restituito il BLOB e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="29435-279">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="29435-280">Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-280">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="29435-281">Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.</span><span class="sxs-lookup"><span data-stu-id="29435-281">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="29435-282">Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="29435-282">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="29435-283">In Storage Explorer (portale di Azure) per ogni file:</span><span class="sxs-lookup"><span data-stu-id="29435-283">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="29435-284">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="29435-284">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="29435-285">Impostare **ContentType** e selezionare il pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="29435-285">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="29435-286">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="29435-286">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="29435-287">Nginx</span><span class="sxs-lookup"><span data-stu-id="29435-287">Nginx</span></span>

<span data-ttu-id="29435-288">Il `nginx.conf` file seguente è stato semplificato per illustrare come configurare Nginx per inviare il `index.html` file ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="29435-288">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="29435-289">Quando si imposta il [limite di velocità](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) di espansione nginx con, le [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) Blazor WebAssembly app possono richiedere un `burst` valore di parametro di grandi dimensioni per contenere il numero relativamente elevato di richieste effettuate da un'app.</span><span class="sxs-lookup"><span data-stu-id="29435-289">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="29435-290">Inizialmente, impostare il valore su almeno 60:</span><span class="sxs-lookup"><span data-stu-id="29435-290">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="29435-291">Aumentare il valore se browser Developer Tools o uno strumento di traffico di rete indica che le richieste ricevono un codice di stato *503-servizio non disponibile* .</span><span class="sxs-lookup"><span data-stu-id="29435-291">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="29435-292">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="29435-292">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="29435-293">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="29435-293">Nginx in Docker</span></span>

<span data-ttu-id="29435-294">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="29435-294">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="29435-295">Aggiornare Dockerfile per copiare il `nginx.config` file nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="29435-295">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="29435-296">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="29435-296">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="29435-297">Apache</span><span class="sxs-lookup"><span data-stu-id="29435-297">Apache</span></span>

<span data-ttu-id="29435-298">Per distribuire un' Blazor WebAssembly app in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="29435-298">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="29435-299">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="29435-299">Create the Apache configuration file.</span></span> <span data-ttu-id="29435-300">L'esempio seguente è un file di configurazione semplificato ( `blazorapp.config` ):</span><span class="sxs-lookup"><span data-stu-id="29435-300">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="29435-301">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="29435-301">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="29435-302">Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="29435-302">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="29435-303">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="29435-303">Restart the Apache service.</span></span>

<span data-ttu-id="29435-304">Per altre informazioni, vedere [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="29435-304">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="29435-305">GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="29435-305">GitHub Pages</span></span>

<span data-ttu-id="29435-306">Per gestire le riscritture URL, aggiungere un `wwwroot/404.html` file con uno script che gestisce il reindirizzamento della richiesta alla `index.html` pagina.</span><span class="sxs-lookup"><span data-stu-id="29435-306">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="29435-307">Per un esempio, vedere il [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="29435-307">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="29435-308">[Sito Live](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="29435-308">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="29435-309">Quando si usa un sito del progetto anziché un sito dell'organizzazione, aggiornare il `<base>` tag in `wwwroot/index.html` .</span><span class="sxs-lookup"><span data-stu-id="29435-309">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="29435-310">Impostare il `href` valore dell'attributo sul nome del repository GitHub con una barra finale (ad esempio, `/my-repository/` ).</span><span class="sxs-lookup"><span data-stu-id="29435-310">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="29435-311">Nel [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages)la base `href` viene aggiornata in fase di pubblicazione dal [ `.github/workflows/main.yml` file di configurazione](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span><span class="sxs-lookup"><span data-stu-id="29435-311">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="29435-312">Il [ Blazor repository GitHub SteveSandersonMS/OnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) non è di proprietà, è stato mantenuto o supportato da .NET Foundation o Microsoft.</span><span class="sxs-lookup"><span data-stu-id="29435-312">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="29435-313">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="29435-313">Host configuration values</span></span>

<span data-ttu-id="29435-314">le [ Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="29435-314">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="29435-315">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="29435-315">Content root</span></span>

<span data-ttu-id="29435-316">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="29435-316">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="29435-317">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="29435-317">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="29435-318">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29435-318">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="29435-319">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="29435-319">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="29435-320">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="29435-320">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="29435-321">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="29435-321">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="29435-322">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="29435-322">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="29435-323">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="29435-323">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="29435-324">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="29435-324">Path base</span></span>

<span data-ttu-id="29435-325">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="29435-325">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="29435-326">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="29435-326">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="29435-327">Per altre informazioni, vedere [percorso di base dell'app](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="29435-327">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="29435-328">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="29435-328">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="29435-329">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="29435-329">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="29435-330">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29435-330">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="29435-331">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="29435-331">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="29435-332">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="29435-332">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="29435-333">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="29435-333">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="29435-334">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="29435-334">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="29435-335">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="29435-335">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="29435-336">URL</span><span class="sxs-lookup"><span data-stu-id="29435-336">URLs</span></span>

<span data-ttu-id="29435-337">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="29435-337">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="29435-338">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="29435-338">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="29435-339">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="29435-339">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="29435-340">Aggiungere una voce al file dell'app `launchSettings.json` nel profilo di **IIS Express** .</span><span class="sxs-lookup"><span data-stu-id="29435-340">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="29435-341">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="29435-341">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="29435-342">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="29435-342">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="29435-343">L'impostazione dell'argomento nella pagina delle proprietà di Visual Studio consente di aggiungere l'argomento al `launchSettings.json` file.</span><span class="sxs-lookup"><span data-stu-id="29435-343">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="29435-344">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="29435-344">Configure the Linker</span></span>

<span data-ttu-id="29435-345">Blazor esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="29435-345">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="29435-346">Per altre informazioni, vedere <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="29435-346">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="29435-347">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="29435-347">Custom boot resource loading</span></span>

<span data-ttu-id="29435-348">Un' Blazor WebAssembly app può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="29435-348">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="29435-349">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-349">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="29435-350">Consente agli utenti di caricare risorse statiche, ad esempio dati del fuso orario o `dotnet.wasm` da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="29435-350">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="29435-351">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="29435-351">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="29435-352">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="29435-352">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="29435-353">`loadBootResource` i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="29435-353">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="29435-354">Parametro</span><span class="sxs-lookup"><span data-stu-id="29435-354">Parameter</span></span>    | <span data-ttu-id="29435-355">Descrizione</span><span class="sxs-lookup"><span data-stu-id="29435-355">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="29435-356">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="29435-356">The type of the resource.</span></span> <span data-ttu-id="29435-357">Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="29435-357">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="29435-358">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="29435-358">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="29435-359">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="29435-359">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="29435-360">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="29435-360">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="29435-361">`loadBootResource` restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="29435-361">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="29435-362">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="29435-362">URI string.</span></span> <span data-ttu-id="29435-363">Nell'esempio seguente ( `wwwroot/index.html` ), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="29435-363">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="29435-364">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="29435-364">Timezone data</span></span>

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

* <span data-ttu-id="29435-365">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="29435-365">`Promise<Response>`.</span></span> <span data-ttu-id="29435-366">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="29435-366">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="29435-367">L'esempio seguente ( `wwwroot/index.html` ) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="29435-367">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="29435-368">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="29435-368">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="29435-369">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="29435-369">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="29435-370">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="29435-370">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="29435-371">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="29435-371">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="29435-372">I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="29435-372">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="29435-373">Modificare l'estensione di file DLL</span><span class="sxs-lookup"><span data-stu-id="29435-373">Change the filename extension of DLL files</span></span>

<span data-ttu-id="29435-374">Se è necessario modificare le estensioni dei file pubblicati dell'app `.dll` , seguire le istruzioni riportate in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="29435-374">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="29435-375">Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare `.dll` i file in modo da usare un'estensione di file diversa.</span><span class="sxs-lookup"><span data-stu-id="29435-375">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="29435-376">Specificare come destinazione i `.dll` file nella `wwwroot` directory dell'output pubblicato dell'app (ad esempio, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).</span><span class="sxs-lookup"><span data-stu-id="29435-376">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="29435-377">Negli esempi seguenti, `.dll` i file vengono rinominati per l'utilizzo dell' `.bin` estensione di file.</span><span class="sxs-lookup"><span data-stu-id="29435-377">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="29435-378">In Windows:</span><span class="sxs-lookup"><span data-stu-id="29435-378">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="29435-379">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29435-379">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="29435-380">In Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="29435-380">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="29435-381">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29435-381">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="29435-382">Per usare un'estensione di file diversa da `.bin` , sostituire `.bin` nei comandi precedenti.</span><span class="sxs-lookup"><span data-stu-id="29435-382">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="29435-383">Per risolvere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="29435-383">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="29435-384">Rimuovere i `blazor.boot.json.gz` file compressi e `blazor.boot.json.br` .</span><span class="sxs-lookup"><span data-stu-id="29435-384">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="29435-385">La compressione è disabilitata con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="29435-385">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="29435-386">Ricomprimere il `blazor.boot.json` file aggiornato.</span><span class="sxs-lookup"><span data-stu-id="29435-386">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="29435-387">Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso.</span><span class="sxs-lookup"><span data-stu-id="29435-387">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="29435-388">Rimuovere o ricomprimere `wwwroot/service-worker-assets.js.br` e `wwwroot/service-worker-assets.js.gz` .</span><span class="sxs-lookup"><span data-stu-id="29435-388">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="29435-389">In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.</span><span class="sxs-lookup"><span data-stu-id="29435-389">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="29435-390">Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="29435-390">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="29435-391">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="29435-391">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="29435-392">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="29435-392">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="29435-393">Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="29435-393">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="29435-394">Quando si rinominano e si caricano in modalità lazy gli stessi assembly, vedere le istruzioni in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .</span><span class="sxs-lookup"><span data-stu-id="29435-394">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

<span data-ttu-id="29435-395">Per fornire commenti e suggerimenti, vedere [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="29435-395">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
