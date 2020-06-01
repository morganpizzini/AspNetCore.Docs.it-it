---
<span data-ttu-id="23e5b-101">title: "host and deploy ASP.NET Core Blazor webassembly" Author: guardrex Description: "informazioni su come ospitare e distribuire un' Blazor app usando ASP.NET Core, le reti per la distribuzione di contenuti (CDN), i file server e le pagine di GitHub".</span><span class="sxs-lookup"><span data-stu-id="23e5b-101">title: 'Host and deploy ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to host and deploy a Blazor app using ASP.NET Core, Content Delivery Networks (CDN), file servers, and GitHub Pages.'</span></span>
<span data-ttu-id="23e5b-102">monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 05/28/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="23e5b-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:</span></span>
- <span data-ttu-id="23e5b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23e5b-103">'Blazor'</span></span>
- <span data-ttu-id="23e5b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23e5b-104">'Identity'</span></span>
- <span data-ttu-id="23e5b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23e5b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="23e5b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23e5b-106">'Razor'</span></span>
- <span data-ttu-id="23e5b-107">' SignalR ' UID: host-and-deploy/Blazer/webassembly</span><span class="sxs-lookup"><span data-stu-id="23e5b-107">'SignalR' uid: host-and-deploy/blazor/webassembly</span></span>

---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="23e5b-108">Ospitare e distribuire ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="23e5b-108">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="23e5b-109">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)e [Safia Amodio](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="23e5b-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="23e5b-110">Con il [ Blazor modello di hosting webassembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="23e5b-110">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="23e5b-111">L' Blazor app, le relative dipendenze e il Runtime .NET vengono scaricati nel browser in parallelo.</span><span class="sxs-lookup"><span data-stu-id="23e5b-111">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="23e5b-112">L'app viene eseguita direttamente nel thread dell'interfaccia utente del browser.</span><span class="sxs-lookup"><span data-stu-id="23e5b-112">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="23e5b-113">Sono supportate le strategie di distribuzione seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-113">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="23e5b-114">L' Blazor app viene gestita da un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23e5b-114">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="23e5b-115">Questa strategia viene trattata nella sezione [Distribuzione ospitata con ASP.NET Core](#hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="23e5b-115">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="23e5b-116">L' Blazor app si trova in un server Web o in un servizio di hosting statico, in cui .NET non viene usato per gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-116">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="23e5b-117">Questa strategia è illustrata nella sezione relativa alla [distribuzione autonoma](#standalone-deployment) , che include informazioni sull'hosting di un' Blazor app webassembly come una sub-app IIS.</span><span class="sxs-lookup"><span data-stu-id="23e5b-117">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="precompression"></a><span data-ttu-id="23e5b-118">Precompressione</span><span class="sxs-lookup"><span data-stu-id="23e5b-118">Precompression</span></span>

<span data-ttu-id="23e5b-119">Quando Blazor viene pubblicata un'app webassembly, l'output viene precompresso per ridurre le dimensioni dell'app e rimuovere la necessità di compressione del runtime.</span><span class="sxs-lookup"><span data-stu-id="23e5b-119">When a Blazor WebAssembly app is published, the output is precompressed to reduce the app's size and remove the need for runtime compression.</span></span> <span data-ttu-id="23e5b-120">Vengono utilizzati gli algoritmi di compressione seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-120">The following compression algorithms are used:</span></span>

* <span data-ttu-id="23e5b-121">[Brotli](https://tools.ietf.org/html/rfc7932) (livello massimo)</span><span class="sxs-lookup"><span data-stu-id="23e5b-121">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="23e5b-122">Gzip</span><span class="sxs-lookup"><span data-stu-id="23e5b-122">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="23e5b-123">Per disabilitare la compressione, aggiungere la `BlazorEnableCompression` Proprietà MSBuild al file di progetto dell'app e impostare il valore su `false` :</span><span class="sxs-lookup"><span data-stu-id="23e5b-123">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="23e5b-124">Per la configurazione della compressione IIS *Web. config* , vedere la sezione relativa alla [compressione IIS: Brotli e gzip](#brotli-and-gzip-compression) .</span><span class="sxs-lookup"><span data-stu-id="23e5b-124">For IIS *web.config* compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="23e5b-125">Riscrivere gli URL per il routing corretto</span><span class="sxs-lookup"><span data-stu-id="23e5b-125">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="23e5b-126">Il routing delle richieste per i componenti della pagina in un' Blazor app webassembly non è semplice come il routing delle richieste in un Blazor Server, un'app ospitata.</span><span class="sxs-lookup"><span data-stu-id="23e5b-126">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="23e5b-127">Si consideri un' Blazor app webassembly con due componenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-127">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="23e5b-128">*Main. Razor*: carica alla radice dell'app e contiene un collegamento al `About` componente ( `href="About"` ).</span><span class="sxs-lookup"><span data-stu-id="23e5b-128">*Main.razor*: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="23e5b-129">*About. Razor*: `About` Component.</span><span class="sxs-lookup"><span data-stu-id="23e5b-129">*About.razor*: `About` component.</span></span>

<span data-ttu-id="23e5b-130">Quando viene richiesto il documento predefinito dell'app usando la barra degli indirizzi del browser (ad esempio, `https://www.contoso.com/`):</span><span class="sxs-lookup"><span data-stu-id="23e5b-130">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="23e5b-131">Il browser invia una richiesta.</span><span class="sxs-lookup"><span data-stu-id="23e5b-131">The browser makes a request.</span></span>
1. <span data-ttu-id="23e5b-132">Viene restituita la pagina predefinita, di solito *index.html*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-132">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="23e5b-133">*index.html* esegue il bootstrap dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-133">*index.html* bootstraps the app.</span></span>
1. Blazor<span data-ttu-id="23e5b-134">il router viene caricato e il componente viene sottoposto a Razor `Main` rendering.</span><span class="sxs-lookup"><span data-stu-id="23e5b-134">'s router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="23e5b-135">Nella pagina principale, selezionando il collegamento al `About` componente funziona sul client perché il Blazor router interrompe il browser per effettuare una richiesta su Internet per `www.contoso.com` e serve il componente di cui è stato `About` eseguito il rendering `About` .</span><span class="sxs-lookup"><span data-stu-id="23e5b-135">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="23e5b-136">Tutte le richieste per gli endpoint interni *nell' Blazor app webassembly* funzionano allo stesso modo: le richieste non attivano richieste basate su browser a risorse ospitate su server su Internet.</span><span class="sxs-lookup"><span data-stu-id="23e5b-136">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="23e5b-137">Le richieste vengono gestite internamente dal router.</span><span class="sxs-lookup"><span data-stu-id="23e5b-137">The router handles the requests internally.</span></span>

<span data-ttu-id="23e5b-138">Se una richiesta viene effettuata usando la barra degli indirizzi del browser per `www.contoso.com/About`, la richiesta ha esito negativo.</span><span class="sxs-lookup"><span data-stu-id="23e5b-138">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="23e5b-139">La risorsa non esiste nell'host Internet dell'app, quindi viene restituita la risposta *404 non trovato*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-139">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="23e5b-140">Dato che i browser inviano le richieste agli host basati su Internet per le pagine sul lato client, i server Web e i servizi di hosting devono riscrivere tutte le richieste per le risorse che non si trovano fisicamente nel server per la pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-140">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="23e5b-141">Quando viene restituito *index. html* , il router dell'app Blazor accetta e risponde con la risorsa corretta.</span><span class="sxs-lookup"><span data-stu-id="23e5b-141">When *index.html* is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="23e5b-142">Quando si esegue la distribuzione in un server IIS, è possibile usare il modulo URL Rewrite con il file *Web. config* pubblicato dall'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-142">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published *web.config* file.</span></span> <span data-ttu-id="23e5b-143">Per ulteriori informazioni, vedere la sezione [IIS](#iis) .</span><span class="sxs-lookup"><span data-stu-id="23e5b-143">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="23e5b-144">Distribuzione ospitata con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23e5b-144">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="23e5b-145">Una *distribuzione ospitata* serve l' Blazor app webassembly nei browser da un' [app ASP.NET Core](xref:index) eseguita in un server Web.</span><span class="sxs-lookup"><span data-stu-id="23e5b-145">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="23e5b-146">L' Blazor app webassembly client viene pubblicata nella cartella */bin/Release/{Target Framework}/Publish/wwwroot* dell'app Server, insieme a qualsiasi altra risorsa Web statica dell'app Server.</span><span class="sxs-lookup"><span data-stu-id="23e5b-146">The client Blazor WebAssembly app is published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="23e5b-147">Le due app vengono distribuite insieme.</span><span class="sxs-lookup"><span data-stu-id="23e5b-147">The two apps are deployed together.</span></span> <span data-ttu-id="23e5b-148">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23e5b-148">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="23e5b-149">Per una distribuzione ospitata, Visual Studio include il modello di progetto di \*\* Blazor app webassembly\*\* ( `blazorwasm` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ) con l'opzione **Hosted** selezionata ( `-ho|--hosted` quando si usa il `dotnet new` comando).</span><span class="sxs-lookup"><span data-stu-id="23e5b-149">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) with the **Hosted** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="23e5b-150">Per altre informazioni su hosting e distribuzione di app ASP.NET Core, vedere <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="23e5b-150">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="23e5b-151">Per informazioni sulla distribuzione in Servizio app di Azure, vedere <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="23e5b-151">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="23e5b-152">Distribuzione autonoma</span><span class="sxs-lookup"><span data-stu-id="23e5b-152">Standalone deployment</span></span>

<span data-ttu-id="23e5b-153">Una *distribuzione autonoma* serve l' Blazor app webassembly come un set di file statici richiesti direttamente dai client.</span><span class="sxs-lookup"><span data-stu-id="23e5b-153">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="23e5b-154">Qualsiasi file server statico è in grado di gestire l' Blazor app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-154">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="23e5b-155">Le risorse di distribuzione autonome vengono pubblicate nella cartella */bin/Release/{Target Framework}/Publish/wwwroot*</span><span class="sxs-lookup"><span data-stu-id="23e5b-155">Standalone deployment assets are published into the */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="23e5b-156">Servizio app di Azure</span><span class="sxs-lookup"><span data-stu-id="23e5b-156">Azure App Service</span></span>

Blazor<span data-ttu-id="23e5b-157">Le app webassembly possono essere distribuite in app Azure Services in Windows, che ospita l'app in [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="23e5b-157"> WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="23e5b-158">La distribuzione Blazor di un'app webassembly autonoma nel servizio app Azure per Linux non è attualmente supportata.</span><span class="sxs-lookup"><span data-stu-id="23e5b-158">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="23e5b-159">Un'immagine server Linux per ospitare l'app non è disponibile in questo momento.</span><span class="sxs-lookup"><span data-stu-id="23e5b-159">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="23e5b-160">Il lavoro è in corso per abilitare questo scenario.</span><span class="sxs-lookup"><span data-stu-id="23e5b-160">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="23e5b-161">IIS</span><span class="sxs-lookup"><span data-stu-id="23e5b-161">IIS</span></span>

<span data-ttu-id="23e5b-162">IIS è un file server statico idoneo per le Blazor app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-162">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="23e5b-163">Per configurare IIS per l'hosting Blazor , vedere [la pagina relativa alla creazione di un sito Web statico in IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="23e5b-163">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="23e5b-164">Gli asset pubblicati vengono creati nella cartella */bin/Release/{FRAMEWORK DESTINAZIONE}/publish*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-164">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="23e5b-165">Ospitare il contenuto della cartella *publish* nel server Web o nel servizio di hosting.</span><span class="sxs-lookup"><span data-stu-id="23e5b-165">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="23e5b-166">web.config</span><span class="sxs-lookup"><span data-stu-id="23e5b-166">web.config</span></span>

<span data-ttu-id="23e5b-167">Quando Blazor viene pubblicato un progetto, viene creato un file *Web. config* con la seguente configurazione di IIS:</span><span class="sxs-lookup"><span data-stu-id="23e5b-167">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="23e5b-168">Vengono impostati tipi MIME per le estensioni di file seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-168">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="23e5b-169">*. dll*:`application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="23e5b-169">*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="23e5b-170">*. JSON*:`application/json`</span><span class="sxs-lookup"><span data-stu-id="23e5b-170">*.json*: `application/json`</span></span>
  * <span data-ttu-id="23e5b-171">*. WASM*:`application/wasm`</span><span class="sxs-lookup"><span data-stu-id="23e5b-171">*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="23e5b-172">*. WOFF*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="23e5b-172">*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="23e5b-173">*. woff2*:`application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="23e5b-173">*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="23e5b-174">Viene abilitata la compressione HTTP per i tipi MIME seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-174">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="23e5b-175">Vengono stabilite le regole URL Rewrite Module:</span><span class="sxs-lookup"><span data-stu-id="23e5b-175">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="23e5b-176">Serve la sottodirectory in cui risiedono gli asset statici dell'app (*wwwroot/{Path richiesto}*).</span><span class="sxs-lookup"><span data-stu-id="23e5b-176">Serve the sub-directory where the app's static assets reside (*wwwroot/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="23e5b-177">Creare il routing di fallback SPA in modo che le richieste di risorse non di file vengano reindirizzate al documento predefinito dell'app nella cartella Asset statici (*wwwroot/index.html*).</span><span class="sxs-lookup"><span data-stu-id="23e5b-177">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*wwwroot/index.html*).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="23e5b-178">Usare un file Web. config personalizzato</span><span class="sxs-lookup"><span data-stu-id="23e5b-178">Use a custom web.config</span></span>

<span data-ttu-id="23e5b-179">Per usare un file *Web. config* personalizzato, inserire il file *Web. config* personalizzato alla radice della cartella del progetto e pubblicare il progetto.</span><span class="sxs-lookup"><span data-stu-id="23e5b-179">To use a custom *web.config* file, place the custom *web.config* file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="23e5b-180">Installare URL Rewrite Module</span><span class="sxs-lookup"><span data-stu-id="23e5b-180">Install the URL Rewrite Module</span></span>

<span data-ttu-id="23e5b-181">[URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) è necessario per riscrivere gli URL.</span><span class="sxs-lookup"><span data-stu-id="23e5b-181">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="23e5b-182">Il modulo non viene installato per impostazione predefinita e non è disponibile per l'installazione come funzionalità del servizio ruolo Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="23e5b-182">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="23e5b-183">Il modulo deve essere scaricato dal sito Web IIS.</span><span class="sxs-lookup"><span data-stu-id="23e5b-183">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="23e5b-184">Usare Installazione guidata piattaforma Web per installare il modulo:</span><span class="sxs-lookup"><span data-stu-id="23e5b-184">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="23e5b-185">In locale, passare alla [pagina di download di URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="23e5b-185">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="23e5b-186">Per la versione in lingua inglese selezionare **WebPI** per scaricare il programma di installazione di Installazione guidata piattaforma Web.</span><span class="sxs-lookup"><span data-stu-id="23e5b-186">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="23e5b-187">Per altre lingue, selezionare l'architettura appropriata per il server (x86 o x64) per scaricare il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="23e5b-187">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="23e5b-188">Copiare il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="23e5b-188">Copy the installer to the server.</span></span> <span data-ttu-id="23e5b-189">Eseguire il programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="23e5b-189">Run the installer.</span></span> <span data-ttu-id="23e5b-190">Selezionare il pulsante **Installa** e accettare le condizioni di licenza.</span><span class="sxs-lookup"><span data-stu-id="23e5b-190">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="23e5b-191">Al termine dell'installazione non è necessario un riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="23e5b-191">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="23e5b-192">Configurare il sito Web</span><span class="sxs-lookup"><span data-stu-id="23e5b-192">Configure the website</span></span>

<span data-ttu-id="23e5b-193">Impostare il **percorso fisico** del sito Web sulla cartella dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-193">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="23e5b-194">La cartella contiene:</span><span class="sxs-lookup"><span data-stu-id="23e5b-194">The folder contains:</span></span>

* <span data-ttu-id="23e5b-195">Il file *web.config* usato da IIS per configurare il sito Web, inclusi le regole di reindirizzamento richieste e i tipi di contenuto di file.</span><span class="sxs-lookup"><span data-stu-id="23e5b-195">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="23e5b-196">Cartella degli asset statici dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-196">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="23e5b-197">Ospitare come applicazione secondaria IIS</span><span class="sxs-lookup"><span data-stu-id="23e5b-197">Host as an IIS sub-app</span></span>

<span data-ttu-id="23e5b-198">Se un'app autonoma è ospitata come una sub-app IIS, eseguire una delle operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-198">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="23e5b-199">Disabilitare il gestore del modulo ASP.NET Core ereditato.</span><span class="sxs-lookup"><span data-stu-id="23e5b-199">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="23e5b-200">Rimuovere il gestore nel Blazor file *Web. config* pubblicato dall'app aggiungendo una `<handlers>` sezione al file:</span><span class="sxs-lookup"><span data-stu-id="23e5b-200">Remove the handler in the Blazor app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="23e5b-201">Disabilitare l'ereditarietà della sezione radice (padre) dell'app `<system.webServer>` usando un `<location>` elemento con `inheritInChildApplications` impostato su `false` :</span><span class="sxs-lookup"><span data-stu-id="23e5b-201">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="23e5b-202">La rimozione del gestore o la disabilitazione dell'ereditarietà viene eseguita oltre alla [configurazione del percorso di base dell'applicazione](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="23e5b-202">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span> <span data-ttu-id="23e5b-203">Impostare il percorso di base dell'app nel file *index.html* dell'app sull'alias IIS usato durante la configurazione della sottoapp in IIS.</span><span class="sxs-lookup"><span data-stu-id="23e5b-203">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="23e5b-204">Compressione Brotli e gzip</span><span class="sxs-lookup"><span data-stu-id="23e5b-204">Brotli and Gzip compression</span></span>

<span data-ttu-id="23e5b-205">IIS può essere configurato tramite *Web. config* per gestire gli asset compressi Brotli o gzip Blazor .</span><span class="sxs-lookup"><span data-stu-id="23e5b-205">IIS can be configured via *web.config* to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="23e5b-206">Per una configurazione di esempio, vedere [Web. config](webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="23e5b-206">For an example configuration, see [web.config](webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="23e5b-207">Risoluzione dei problemi</span><span class="sxs-lookup"><span data-stu-id="23e5b-207">Troubleshooting</span></span>

<span data-ttu-id="23e5b-208">Se si riceve un *errore interno del server 500* e Gestione IIS genera errori durante il tentativo di accedere alla configurazione del sito Web, verificare che sia installato URL Rewrite Module.</span><span class="sxs-lookup"><span data-stu-id="23e5b-208">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="23e5b-209">Quando il modulo non è installato, il file *web.config* non può essere analizzato da IIS.</span><span class="sxs-lookup"><span data-stu-id="23e5b-209">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="23e5b-210">In questo modo si impedisce al gestore IIS di caricare la configurazione del sito Web e il sito Web dai Blazor file statici del servizio.</span><span class="sxs-lookup"><span data-stu-id="23e5b-210">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="23e5b-211">Per altre informazioni sulla risoluzione dei problemi relativi alle distribuzioni in IIS, vedere <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="23e5b-211">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="23e5b-212">Archiviazione di Azure</span><span class="sxs-lookup"><span data-stu-id="23e5b-212">Azure Storage</span></span>

<span data-ttu-id="23e5b-213">L'hosting di file statici di [archiviazione di Azure](/azure/storage/) consente l'hosting di app senza server Blazor .</span><span class="sxs-lookup"><span data-stu-id="23e5b-213">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="23e5b-214">Sono supportati nomi di dominio personalizzati, la rete per la distribuzione di contenuti (rete CDN) di Azure e HTTPS.</span><span class="sxs-lookup"><span data-stu-id="23e5b-214">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="23e5b-215">Quando il servizio BLOB è abilitato per l'hosting di siti Web statici in un account di archiviazione:</span><span class="sxs-lookup"><span data-stu-id="23e5b-215">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="23e5b-216">Impostare **Nome del documento di indice** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-216">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="23e5b-217">Impostare **Percorso del documento di errore** su `index.html`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-217">Set the **Error document path** to `index.html`.</span></span> Razor<span data-ttu-id="23e5b-218">i componenti e altri endpoint non di file non si trovano in percorsi fisici nel contenuto statico archiviato dal servizio BLOB.</span><span class="sxs-lookup"><span data-stu-id="23e5b-218"> components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="23e5b-219">Quando viene ricevuta una richiesta per una di queste risorse che il Blazor router deve gestire, l'errore *404 non trovato* generato dal servizio BLOB instrada la richiesta al **percorso del documento di errore**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-219">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="23e5b-220">Viene restituito il BLOB *index. html* e il Blazor router carica ed elabora il percorso.</span><span class="sxs-lookup"><span data-stu-id="23e5b-220">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="23e5b-221">Se i file non vengono caricati in fase di esecuzione a causa di tipi MIME non appropriati nelle intestazioni dei file `Content-Type` , eseguire una delle azioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-221">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="23e5b-222">Configurare gli strumenti per impostare i tipi MIME corretti ( `Content-Type` intestazioni) quando vengono distribuiti i file.</span><span class="sxs-lookup"><span data-stu-id="23e5b-222">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="23e5b-223">Modificare i tipi MIME ( `Content-Type` intestazioni) per i file dopo la distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-223">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="23e5b-224">In Storage Explorer (portale di Azure) per ogni file:</span><span class="sxs-lookup"><span data-stu-id="23e5b-224">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="23e5b-225">Fare clic con il pulsante destro del mouse sul file e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-225">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="23e5b-226">Impostare **ContentType** e selezionare il pulsante **Salva** .</span><span class="sxs-lookup"><span data-stu-id="23e5b-226">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="23e5b-227">Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="23e5b-227">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="23e5b-228">Nginx</span><span class="sxs-lookup"><span data-stu-id="23e5b-228">Nginx</span></span>

<span data-ttu-id="23e5b-229">Il file *nginx. conf* seguente è stato semplificato per illustrare come configurare Nginx per inviare il file *index. html* ogni volta che non riesce a trovare un file corrispondente sul disco.</span><span class="sxs-lookup"><span data-stu-id="23e5b-229">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="23e5b-230">Per altre informazioni sulla configurazione del server Web Nginx in ambiente di produzione, vedere [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) (Creazione di file di configurazione NGINX Plus e NGINX).</span><span class="sxs-lookup"><span data-stu-id="23e5b-230">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="23e5b-231">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="23e5b-231">Nginx in Docker</span></span>

<span data-ttu-id="23e5b-232">Per ospitare Blazor in Docker con nginx, configurare Dockerfile per l'uso dell'immagine nginx basata su Alpine.</span><span class="sxs-lookup"><span data-stu-id="23e5b-232">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="23e5b-233">Aggiornare il Dockerfile per copiare il file *nginx.config* nel contenitore.</span><span class="sxs-lookup"><span data-stu-id="23e5b-233">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="23e5b-234">Aggiungere una riga al Dockerfile, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="23e5b-234">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="23e5b-235">Apache</span><span class="sxs-lookup"><span data-stu-id="23e5b-235">Apache</span></span>

<span data-ttu-id="23e5b-236">Per distribuire un' Blazor app webassembly in CentOS 7 o versione successiva:</span><span class="sxs-lookup"><span data-stu-id="23e5b-236">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="23e5b-237">Creare il file di configurazione Apache.</span><span class="sxs-lookup"><span data-stu-id="23e5b-237">Create the Apache configuration file.</span></span> <span data-ttu-id="23e5b-238">L'esempio seguente è un file di configurazione semplificato (*blazorapp. config*):</span><span class="sxs-lookup"><span data-stu-id="23e5b-238">The following example is a simplified configuration file (*blazorapp.config*):</span></span>

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

1. <span data-ttu-id="23e5b-239">Inserire il file di configurazione Apache nella `/etc/httpd/conf.d/` Directory, ovvero la directory di configurazione predefinita di Apache in CentOS 7.</span><span class="sxs-lookup"><span data-stu-id="23e5b-239">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="23e5b-240">Inserire i file dell'app nella `/var/www/blazorapp` Directory (il percorso specificato `DocumentRoot` nel file di configurazione).</span><span class="sxs-lookup"><span data-stu-id="23e5b-240">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="23e5b-241">Riavviare il servizio Apache.</span><span class="sxs-lookup"><span data-stu-id="23e5b-241">Restart the Apache service.</span></span>

<span data-ttu-id="23e5b-242">Per ulteriori informazioni, vedere [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) e [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="23e5b-242">For more information, see [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="23e5b-243">Pagine di GitHub</span><span class="sxs-lookup"><span data-stu-id="23e5b-243">GitHub Pages</span></span>

<span data-ttu-id="23e5b-244">Per gestire le riscritture degli URL, aggiungere un file *404.html* con uno script che gestisce il reindirizzamento della richiesta alla pagina *index.html*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-244">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="23e5b-245">Per un esempio di implementazione fornito dalla community, vedere [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) (App a pagina singola per pagine GitHub) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="23e5b-245">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="23e5b-246">È possibile visualizzare un esempio d'uso dell'approccio della community in [blazor-demo/blazor-demo.github.io su GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([sito live](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="23e5b-246">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="23e5b-247">Quando si usa un sito di progetto anziché un sito dell'organizzazione, aggiungere o aggiornare il tag `<base>` in *index.html*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-247">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="23e5b-248">Impostare il valore dell'attributo `href` sul nome del repository GitHub con una barra finale (ad esempio, `my-repository/`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-248">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="23e5b-249">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="23e5b-249">Host configuration values</span></span>

<span data-ttu-id="23e5b-250">Le [ Blazor app webassembly](xref:blazor/hosting-models#blazor-webassembly) possono accettare i valori di configurazione host seguenti come argomenti della riga di comando in fase di esecuzione nell'ambiente di sviluppo.</span><span class="sxs-lookup"><span data-stu-id="23e5b-250">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="23e5b-251">Radice del contenuto</span><span class="sxs-lookup"><span data-stu-id="23e5b-251">Content root</span></span>

<span data-ttu-id="23e5b-252">L' `--contentroot` argomento imposta il percorso assoluto della directory che contiene i file di contenuto dell'app ([radice del contenuto](xref:fundamentals/index#content-root)).</span><span class="sxs-lookup"><span data-stu-id="23e5b-252">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="23e5b-253">Negli esempi seguenti `/content-root-path` è il percorso radice del contenuto dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-253">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="23e5b-254">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="23e5b-254">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="23e5b-255">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="23e5b-255">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="23e5b-256">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-256">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="23e5b-257">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-257">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="23e5b-258">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-258">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="23e5b-259">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-259">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="23e5b-260">Base del percorso</span><span class="sxs-lookup"><span data-stu-id="23e5b-260">Path base</span></span>

<span data-ttu-id="23e5b-261">L' `--pathbase` argomento imposta il percorso di base dell'app per un'app eseguita localmente con un percorso URL relativo non radice (il `<base>` tag `href` è impostato su un percorso diverso da `/` per la gestione temporanea e la produzione).</span><span class="sxs-lookup"><span data-stu-id="23e5b-261">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="23e5b-262">Negli esempi seguenti `/relative-URL-path` è la base del percorso dell'app.</span><span class="sxs-lookup"><span data-stu-id="23e5b-262">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="23e5b-263">Per altre informazioni, vedere [percorso di base dell'app](xref:host-and-deploy/blazor/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="23e5b-263">For more information, see [App base path](xref:host-and-deploy/blazor/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="23e5b-264">A differenza del percorso specificato per `href` del tag `<base>`, non includere una barra finale (`/`) quando si passa il valore dell'argomento `--pathbase`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-264">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="23e5b-265">Se il percorso di base dell'app non viene specificato nel tag `<base>` come `<base href="/CoolApp/">` (include una barra finale), passare il valore dell'argomento della riga di comando come `--pathbase=/CoolApp` (senza barra finale).</span><span class="sxs-lookup"><span data-stu-id="23e5b-265">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="23e5b-266">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="23e5b-266">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="23e5b-267">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="23e5b-267">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="23e5b-268">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-268">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="23e5b-269">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-269">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="23e5b-270">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-270">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="23e5b-271">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-271">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="23e5b-272">URL</span><span class="sxs-lookup"><span data-stu-id="23e5b-272">URLs</span></span>

<span data-ttu-id="23e5b-273">L'argomento `--urls` imposta gli indirizzi IP o gli indirizzi host con le porte e i protocolli su cui eseguire l'ascolto per le richieste.</span><span class="sxs-lookup"><span data-stu-id="23e5b-273">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="23e5b-274">Passare l'argomento quando si esegue localmente l'app a un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="23e5b-274">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="23e5b-275">Dalla directory dell'app, eseguire:</span><span class="sxs-lookup"><span data-stu-id="23e5b-275">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="23e5b-276">Aggiungere una voce al file *launchSettings.json* dell'app nel profilo **IIS Express**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-276">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="23e5b-277">Questa impostazione viene usata quando l'app viene eseguita con il debugger di Visual Studio e da un prompt dei comandi con `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-277">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="23e5b-278">In Visual Studio specificare l'argomento in **Proprietà**  >  **debug**  >  **argomenti dell'applicazione**.</span><span class="sxs-lookup"><span data-stu-id="23e5b-278">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="23e5b-279">Impostando l'argomento nella pagina delle proprietà di Visual Studio, si aggiunge l'argomento al file *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-279">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a><span data-ttu-id="23e5b-280">Configurare il linker</span><span class="sxs-lookup"><span data-stu-id="23e5b-280">Configure the Linker</span></span>

Blazor<span data-ttu-id="23e5b-281">esegue il collegamento Intermediate Language (IL) a ogni build di rilascio per rimuovere IL linguaggio intermedio non necessario dagli assembly di output.</span><span class="sxs-lookup"><span data-stu-id="23e5b-281"> performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="23e5b-282">Per altre informazioni, vedere <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="23e5b-282">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="23e5b-283">Caricamento di risorse di avvio personalizzate</span><span class="sxs-lookup"><span data-stu-id="23e5b-283">Custom boot resource loading</span></span>

<span data-ttu-id="23e5b-284">Un' Blazor app webassembly può essere inizializzata con la `loadBootResource` funzione per eseguire l'override del meccanismo di caricamento delle risorse di avvio predefinito.</span><span class="sxs-lookup"><span data-stu-id="23e5b-284">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="23e5b-285">Utilizzare `loadBootResource` per gli scenari seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-285">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="23e5b-286">Consentire agli utenti di caricare risorse statiche, ad esempio i dati del fuso orario o *dotnet. WASM* da una rete CDN.</span><span class="sxs-lookup"><span data-stu-id="23e5b-286">Allow users to load static resources, such as timezone data or *dotnet.wasm* from a CDN.</span></span>
* <span data-ttu-id="23e5b-287">Caricare gli assembly compressi usando una richiesta HTTP e decomprimerli sul client per gli host che non supportano il recupero di contenuti compressi dal server.</span><span class="sxs-lookup"><span data-stu-id="23e5b-287">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="23e5b-288">Eseguire l'alias delle risorse con un nome diverso reindirizzando ogni `fetch` richiesta a un nuovo nome.</span><span class="sxs-lookup"><span data-stu-id="23e5b-288">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="23e5b-289">`loadBootResource`i parametri vengono visualizzati nella tabella seguente.</span><span class="sxs-lookup"><span data-stu-id="23e5b-289">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="23e5b-290">Parametro</span><span class="sxs-lookup"><span data-stu-id="23e5b-290">Parameter</span></span>    | <span data-ttu-id="23e5b-291">Descrizione</span><span class="sxs-lookup"><span data-stu-id="23e5b-291">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="23e5b-292">Tipo di risorsa.</span><span class="sxs-lookup"><span data-stu-id="23e5b-292">The type of the resource.</span></span> <span data-ttu-id="23e5b-293">Tipi di consentiti: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata`</span><span class="sxs-lookup"><span data-stu-id="23e5b-293">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="23e5b-294">Nome della risorsa.</span><span class="sxs-lookup"><span data-stu-id="23e5b-294">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="23e5b-295">URI relativo o assoluto della risorsa.</span><span class="sxs-lookup"><span data-stu-id="23e5b-295">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="23e5b-296">Stringa di integrità che rappresenta il contenuto previsto nella risposta.</span><span class="sxs-lookup"><span data-stu-id="23e5b-296">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="23e5b-297">`loadBootResource`restituisce uno dei seguenti elementi per eseguire l'override del processo di caricamento:</span><span class="sxs-lookup"><span data-stu-id="23e5b-297">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="23e5b-298">Stringa URI.</span><span class="sxs-lookup"><span data-stu-id="23e5b-298">URI string.</span></span> <span data-ttu-id="23e5b-299">Nell'esempio seguente (*wwwroot/index.html*), i file seguenti vengono serviti da una rete CDN all'indirizzo `https://my-awesome-cdn.com/` :</span><span class="sxs-lookup"><span data-stu-id="23e5b-299">In the following example (*wwwroot/index.html*), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * <span data-ttu-id="23e5b-300">*dotnet. \* .. JS*</span><span class="sxs-lookup"><span data-stu-id="23e5b-300">*dotnet.\*.js*</span></span>
  * <span data-ttu-id="23e5b-301">*dotnet. WASM*</span><span class="sxs-lookup"><span data-stu-id="23e5b-301">*dotnet.wasm*</span></span>
  * <span data-ttu-id="23e5b-302">Dati TimeZone</span><span class="sxs-lookup"><span data-stu-id="23e5b-302">Timezone data</span></span>

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

* <span data-ttu-id="23e5b-303">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="23e5b-303">`Promise<Response>`.</span></span> <span data-ttu-id="23e5b-304">Passare il `integrity` parametro in un'intestazione per mantenere il comportamento predefinito di controllo dell'integrità.</span><span class="sxs-lookup"><span data-stu-id="23e5b-304">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="23e5b-305">L'esempio seguente (*wwwroot/index.html*) aggiunge un'intestazione HTTP personalizzata alle richieste in uscita e passa il `integrity` parametro tramite alla `fetch` chiamata:</span><span class="sxs-lookup"><span data-stu-id="23e5b-305">The following example (*wwwroot/index.html*) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="23e5b-306">`null`/`undefined`, che comporta il comportamento di caricamento predefinito.</span><span class="sxs-lookup"><span data-stu-id="23e5b-306">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="23e5b-307">Per consentire il caricamento delle risorse tra le origini, le origini esterne devono restituire le intestazioni CORS necessarie per i browser.</span><span class="sxs-lookup"><span data-stu-id="23e5b-307">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="23e5b-308">Per impostazione predefinita, CDNs fornisce in genere le intestazioni obbligatorie.</span><span class="sxs-lookup"><span data-stu-id="23e5b-308">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="23e5b-309">È sufficiente specificare i tipi per i comportamenti personalizzati.</span><span class="sxs-lookup"><span data-stu-id="23e5b-309">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="23e5b-310">I tipi non specificati in `loadBootResource` vengono caricati dal Framework in base ai relativi comportamenti di caricamento predefiniti.</span><span class="sxs-lookup"><span data-stu-id="23e5b-310">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="23e5b-311">Modificare l'estensione di file DLL</span><span class="sxs-lookup"><span data-stu-id="23e5b-311">Change the filename extension of DLL files</span></span>

<span data-ttu-id="23e5b-312">Se è necessario modificare le estensioni del nome file dei file con *estensione dll* pubblicati dell'app, seguire le istruzioni riportate in questa sezione.</span><span class="sxs-lookup"><span data-stu-id="23e5b-312">In case you have a need to change the filename extensions of the app's published *.dll* files, follow the guidance in this section.</span></span>

<span data-ttu-id="23e5b-313">Dopo la pubblicazione dell'app, usare uno script della shell o una pipeline di compilazione DevOps per rinominare i file con estensione *dll* per usare un'estensione di file diversa.</span><span class="sxs-lookup"><span data-stu-id="23e5b-313">After publishing the app, use a shell script or DevOps build pipeline to rename *.dll* files to use a different file extension.</span></span> <span data-ttu-id="23e5b-314">Specificare come destinazione i file con *estensione dll* nella directory *wwwroot* dell'output pubblicato dell'app (ad esempio, *{Content root}/bin/Release/netstandard2.1/Publish/wwwroot*).</span><span class="sxs-lookup"><span data-stu-id="23e5b-314">Target the *.dll* files in the *wwwroot* directory of the app's published output (for example, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).</span></span>

<span data-ttu-id="23e5b-315">Negli esempi seguenti, i file con estensione *dll* vengono rinominati per l'utilizzo dell'estensione di file *bin* .</span><span class="sxs-lookup"><span data-stu-id="23e5b-315">In the following examples, *.dll* files are renamed to use the *.bin* file extension.</span></span>

<span data-ttu-id="23e5b-316">In Windows:</span><span class="sxs-lookup"><span data-stu-id="23e5b-316">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="23e5b-317">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="23e5b-317">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="23e5b-318">In Linux o macOS:</span><span class="sxs-lookup"><span data-stu-id="23e5b-318">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="23e5b-319">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="23e5b-319">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="23e5b-320">Per usare un'estensione di file diversa da *. bin*, sostituire *. bin* nei comandi precedenti.</span><span class="sxs-lookup"><span data-stu-id="23e5b-320">To use a different file extension than *.bin*, replace *.bin* in the preceding commands.</span></span>

<span data-ttu-id="23e5b-321">Per risolvere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* , adottare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="23e5b-321">To address the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="23e5b-322">Rimuovere i file compressi *blazer. boot. JSON. gz* e *blazor.boot.JSON.br* .</span><span class="sxs-lookup"><span data-stu-id="23e5b-322">Remove the compressed *blazor.boot.json.gz* and *blazor.boot.json.br* files.</span></span> <span data-ttu-id="23e5b-323">La compressione è disabilitata con questo approccio.</span><span class="sxs-lookup"><span data-stu-id="23e5b-323">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="23e5b-324">Ricomprimere il file *blazer. boot. JSON* aggiornato.</span><span class="sxs-lookup"><span data-stu-id="23e5b-324">Recompress the updated *blazor.boot.json* file.</span></span>

<span data-ttu-id="23e5b-325">Le linee guida precedenti si applicano anche quando le risorse di lavoro del servizio sono in uso.</span><span class="sxs-lookup"><span data-stu-id="23e5b-325">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="23e5b-326">Rimuovere o ricomprimere *wwwroot/service-worker-assets. js. br* e *wwwroot/service-worker-assets. js. gz*.</span><span class="sxs-lookup"><span data-stu-id="23e5b-326">Remove or recompress *wwwroot/service-worker-assets.js.br* and *wwwroot/service-worker-assets.js.gz*.</span></span> <span data-ttu-id="23e5b-327">In caso contrario, i controlli di integrità dei file avranno esito negativo nel browser.</span><span class="sxs-lookup"><span data-stu-id="23e5b-327">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="23e5b-328">Nell'esempio di Windows seguente viene usato uno script di PowerShell inserito nella radice del progetto.</span><span class="sxs-lookup"><span data-stu-id="23e5b-328">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="23e5b-329">*ChangeDLLExtensions. ps1:*:</span><span class="sxs-lookup"><span data-stu-id="23e5b-329">*ChangeDLLExtensions.ps1:*:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="23e5b-330">Se gli asset di lavoro del servizio sono anche in uso, aggiungere il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="23e5b-330">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="23e5b-331">Nel file di progetto, lo script viene eseguito dopo la pubblicazione dell'app:</span><span class="sxs-lookup"><span data-stu-id="23e5b-331">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

<span data-ttu-id="23e5b-332">Per fornire commenti e suggerimenti, vedere [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span><span class="sxs-lookup"><span data-stu-id="23e5b-332">To provide feedback, visit [aspnetcore/issues #5477](https://github.com/dotnet/aspnetcore/issues/5477).</span></span>
 
