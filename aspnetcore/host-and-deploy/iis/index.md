---
<span data-ttu-id="92388-101">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-102">'Blazor'</span></span>
- <span data-ttu-id="92388-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-103">'Identity'</span></span>
- <span data-ttu-id="92388-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-105">'Razor'</span></span>
- <span data-ttu-id="92388-106">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-106">'SignalR' uid:</span></span> 

---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="92388-107">Host ASP.NET Core in Windows con IIS</span><span class="sxs-lookup"><span data-stu-id="92388-107">Host ASP.NET Core on Windows with IIS</span></span>

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="92388-108">Per un'esercitazione sulla pubblicazione di un'app ASP.NET Core in un server IIS, vedere <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="92388-108">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="92388-109">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-109">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="92388-110">Sistemi operativi supportati</span><span class="sxs-lookup"><span data-stu-id="92388-110">Supported operating systems</span></span>

<span data-ttu-id="92388-111">Sono supportati i sistemi operativi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-111">The following operating systems are supported:</span></span>

* <span data-ttu-id="92388-112">Windows 7 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-112">Windows 7 or later</span></span>
* <span data-ttu-id="92388-113">Windows Server 2012 R2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-113">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="92388-114">Il [server HTTP.sys](xref:fundamentals/servers/httpsys) (chiamato in precedenza WebListener) non funziona in una configurazione proxy inverso con IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="92388-115">È necessario usare il [server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-115">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92388-116">Per informazioni sull'hosting in Azure, vedere <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="92388-116">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="92388-117">Per linee guida per la risoluzione dei problemi, vedere <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="92388-117">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="92388-118">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="92388-118">Supported platforms</span></span>

<span data-ttu-id="92388-119">Sono supportate le app pubblicate per la distribuzione a 32 bit (x86) o a 64 bit (x64).</span><span class="sxs-lookup"><span data-stu-id="92388-119">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="92388-120">Distribuire un'app a 32 bit con .NET Core SDK a 32 bit (x86), a meno che l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-120">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="92388-121">Non richieda lo spazio indirizzi di memoria virtuale più grande disponibile per un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-121">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="92388-122">Non richieda le dimensioni maggiori dello stack IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-122">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="92388-123">Non abbia dipendenze native a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-123">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="92388-124">Per le app pubblicate per 32 bit (x86) è necessario che sia abilitato 32 bit per i pool di applicazioni IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-124">Apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="92388-125">Per ulteriori informazioni, vedere la sezione [creare il sito IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="92388-125">For more information, see the [Create the IIS site](#create-the-iis-site) section.</span></span>

<span data-ttu-id="92388-126">Usare .NET Core SDK a 64 bit (x64) per pubblicare un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-126">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="92388-127">Un runtime a 64 bit deve essere presente nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="92388-127">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="92388-128">Modelli di hosting</span><span class="sxs-lookup"><span data-stu-id="92388-128">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="92388-129">Modello di hosting in-process</span><span class="sxs-lookup"><span data-stu-id="92388-129">In-process hosting model</span></span>

<span data-ttu-id="92388-130">Se si usa l'hosting in-process, un'app ASP.NET Core esegue lo stesso processo del processo di lavoro IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="92388-131">L'hosting in-process offre prestazioni migliori rispetto all'hosting out-of-process perché le richieste non vengono inserite in proxy sulla scheda di loopback, un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer.</span><span class="sxs-lookup"><span data-stu-id="92388-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="92388-132">Per gestire il processo, IIS usa il [servizio Attivazione processo Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="92388-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="92388-133">Il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="92388-133">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="92388-134">Esegue l'inizializzazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-134">Performs app initialization.</span></span>
  * <span data-ttu-id="92388-135">Carica [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="92388-135">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="92388-136">Chiama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="92388-136">Calls `Program.Main`.</span></span>
* <span data-ttu-id="92388-137">Gestisce la durata della richiesta nativa di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-137">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="92388-138">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata in-process:</span><span class="sxs-lookup"><span data-stu-id="92388-138">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting in-process](index/_static/ancm-inprocess.png)

1. <span data-ttu-id="92388-140">Una richiesta arriva dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="92388-140">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="92388-141">Il driver instrada la richiesta nativa IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-141">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="92388-142">Il modulo ASP.NET Core riceve la richiesta nativa e la passa al server HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="92388-142">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="92388-143">Il server HTTP di IIS è un'implementazione di server in-process per IIS che converte la richiesta da nativa a gestita.</span><span class="sxs-lookup"><span data-stu-id="92388-143">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="92388-144">Dopo l'elaborazione della richiesta da parte del server HTTP IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-144">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="92388-145">La richiesta viene inviata alla pipeline del middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-145">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="92388-146">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-146">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="92388-147">La risposta dell'app viene restituita a IIS tramite il server HTTP di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-147">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="92388-148">IIS invia la risposta al client che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-148">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="92388-149">L'hosting in-process è il consenso esplicito per le app esistenti.</span><span class="sxs-lookup"><span data-stu-id="92388-149">In-process hosting is opt-in for existing apps.</span></span> <span data-ttu-id="92388-150">I modelli Web ASP.NET Core utilizzano il modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="92388-150">The ASP.NET Core web templates use the in-process hosting model.</span></span>

<span data-ttu-id="92388-151">`CreateDefaultBuilder` aggiunge un'istanza di <xref:Microsoft.AspNetCore.Hosting.Server.IServer> chiamando il metodo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> per avviare [CoreCLR](/dotnet/standard/glossary#coreclr) e ospitare l'app all'interno del processo di lavoro IIS (*w3wp.exe* o *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="92388-151">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="92388-152">I test delle prestazioni indicano che l'hosting di un'app .NET Core in-process offre una velocità effettiva delle richieste significativamente superiore rispetto all'hosting dell'app out-of-process o all'inoltro delle richieste a [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-152">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92388-153">Le app pubblicate come un singolo file eseguibile non possono essere caricate dal modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="92388-153">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="92388-154">Modello di hosting out-of-process</span><span class="sxs-lookup"><span data-stu-id="92388-154">Out-of-process hosting model</span></span>

<span data-ttu-id="92388-155">Poiché le app ASP.NET Core vengono eseguite in un processo separato dal processo di lavoro IIS, il modulo ASP.NET Core gestisce la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="92388-155">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="92388-156">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="92388-156">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="92388-157">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="92388-157">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="92388-158">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:</span><span class="sxs-lookup"><span data-stu-id="92388-158">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting out-of-process](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="92388-160">Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="92388-160">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="92388-161">Il driver instrada le richieste a IIS sulla porta configurata del sito Web.</span><span class="sxs-lookup"><span data-stu-id="92388-161">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="92388-162">La porta configurata è generalmente 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-162">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="92388-163">Il modulo Invia le richieste a gheppio su una porta casuale per l'app.</span><span class="sxs-lookup"><span data-stu-id="92388-163">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="92388-164">La porta casuale non è 80 o 443.</span><span class="sxs-lookup"><span data-stu-id="92388-164">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="92388-165">Il modulo ASP.NET Core specifica la porta tramite una variabile di ambiente all'avvio.</span><span class="sxs-lookup"><span data-stu-id="92388-165">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="92388-166">L' <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> estensione configura il server per l'ascolto `http://localhost:{PORT}` .</span><span class="sxs-lookup"><span data-stu-id="92388-166">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="92388-167">Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate.</span><span class="sxs-lookup"><span data-stu-id="92388-167">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="92388-168">Il modulo non supporta l'invio HTTPS.</span><span class="sxs-lookup"><span data-stu-id="92388-168">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="92388-169">Le richieste vengono inviate tramite HTTP anche se ricevute da IIS su HTTPS.</span><span class="sxs-lookup"><span data-stu-id="92388-169">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="92388-170">Quando il gheppio preleva la richiesta dal modulo, la richiesta viene trasmessa alla pipeline del middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-170">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="92388-171">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-171">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="92388-172">Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="92388-172">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="92388-173">La risposta dell'app viene passata di nuovo a IIS, che la inoltra al client HTTP che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-173">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="92388-174">Per indicazioni sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-174">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="92388-175">Per altre informazioni sull'hosting, vedere [Hosting in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="92388-175">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="92388-176">Configurazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="92388-176">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="92388-177">Abilitare i componenti IISIntegration</span><span class="sxs-lookup"><span data-stu-id="92388-177">Enable the IISIntegration components</span></span>

<span data-ttu-id="92388-178">Quando si compila un host in `CreateHostBuilder` (*Program.cs*), chiamare <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> per abilitare l'integrazione con IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-178">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="92388-179">Per altre informazioni su `CreateDefaultBuilder`, vedere <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="92388-179">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="92388-180">Opzioni IIS</span><span class="sxs-lookup"><span data-stu-id="92388-180">IIS options</span></span>

<span data-ttu-id="92388-181">**Modello di hosting in-process**</span><span class="sxs-lookup"><span data-stu-id="92388-181">**In-process hosting model**</span></span>

<span data-ttu-id="92388-182">Per configurare le opzioni del server IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="92388-182">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="92388-183">L'esempio seguente disabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="92388-183">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="92388-184">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-184">Option</span></span>                         | <span data-ttu-id="92388-185">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-185">Default</span></span> | <span data-ttu-id="92388-186">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-186">Setting</span></span> |
| ---
<span data-ttu-id="92388-187">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-188">'Blazor'</span></span>
- <span data-ttu-id="92388-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-189">'Identity'</span></span>
- <span data-ttu-id="92388-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-191">'Razor'</span></span>
- <span data-ttu-id="92388-192">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-193">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-194">'Blazor'</span></span>
- <span data-ttu-id="92388-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-195">'Identity'</span></span>
- <span data-ttu-id="92388-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-197">'Razor'</span></span>
- <span data-ttu-id="92388-198">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-199">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-200">'Blazor'</span></span>
- <span data-ttu-id="92388-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-201">'Identity'</span></span>
- <span data-ttu-id="92388-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-203">'Razor'</span></span>
- <span data-ttu-id="92388-204">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-205">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-206">'Blazor'</span></span>
- <span data-ttu-id="92388-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-207">'Identity'</span></span>
- <span data-ttu-id="92388-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-209">'Razor'</span></span>
- <span data-ttu-id="92388-210">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-211">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-212">'Blazor'</span></span>
- <span data-ttu-id="92388-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-213">'Identity'</span></span>
- <span data-ttu-id="92388-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-215">'Razor'</span></span>
- <span data-ttu-id="92388-216">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-217">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-218">'Blazor'</span></span>
- <span data-ttu-id="92388-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-219">'Identity'</span></span>
- <span data-ttu-id="92388-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-221">'Razor'</span></span>
- <span data-ttu-id="92388-222">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-223">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-224">'Blazor'</span></span>
- <span data-ttu-id="92388-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-225">'Identity'</span></span>
- <span data-ttu-id="92388-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-227">'Razor'</span></span>
- <span data-ttu-id="92388-228">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-229">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-230">'Blazor'</span></span>
- <span data-ttu-id="92388-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-231">'Identity'</span></span>
- <span data-ttu-id="92388-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-233">'Razor'</span></span>
- <span data-ttu-id="92388-234">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-235">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-236">'Blazor'</span></span>
- <span data-ttu-id="92388-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-237">'Identity'</span></span>
- <span data-ttu-id="92388-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-239">'Razor'</span></span>
- <span data-ttu-id="92388-240">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-241">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-242">'Blazor'</span></span>
- <span data-ttu-id="92388-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-243">'Identity'</span></span>
- <span data-ttu-id="92388-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-245">'Razor'</span></span>
- <span data-ttu-id="92388-246">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-247">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-248">'Blazor'</span></span>
- <span data-ttu-id="92388-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-249">'Identity'</span></span>
- <span data-ttu-id="92388-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-251">'Razor'</span></span>
- <span data-ttu-id="92388-252">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-253">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-254">'Blazor'</span></span>
- <span data-ttu-id="92388-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-255">'Identity'</span></span>
- <span data-ttu-id="92388-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-257">'Razor'</span></span>
- <span data-ttu-id="92388-258">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-259">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-260">'Blazor'</span></span>
- <span data-ttu-id="92388-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-261">'Identity'</span></span>
- <span data-ttu-id="92388-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-263">'Razor'</span></span>
- <span data-ttu-id="92388-264">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-264">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-265">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-265">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-266">'Blazor'</span></span>
- <span data-ttu-id="92388-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-267">'Identity'</span></span>
- <span data-ttu-id="92388-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-269">'Razor'</span></span>
- <span data-ttu-id="92388-270">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-270">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-271">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il server IIS imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-271">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-272">Se `false`, il server fornisce solo un'identità per `HttpContext.User` e risponde alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-272">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-273">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-273">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-274">Per altre informazioni, vedere [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-274">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-275">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-275">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="92388-276">| | `AllowSynchronousIO`           | `false` | Indica se l'i/O sincrono è consentito per `HttpContext.Request` e `HttpContext.Response` .</span><span class="sxs-lookup"><span data-stu-id="92388-276">| | `AllowSynchronousIO`           | `false` | Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> <span data-ttu-id="92388-277">| | `MaxRequestBodySize`           | `30000000`  | Ottiene o imposta le dimensioni massime del corpo della richiesta per l'oggetto `HttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="92388-277">| | `MaxRequestBodySize`           | `30000000`  | Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="92388-278">Notare che IIS stesso include il limite `maxAllowedContentLength`, che verrà elaborato prima del set `MaxRequestBodySize` in `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="92388-278">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="92388-279">La modifica di `MaxRequestBodySize` non influisce su `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="92388-279">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="92388-280">Per aumentare `maxAllowedContentLength`, aggiungere una voce in *web.config* per impostare `maxAllowedContentLength` su un valore superiore.</span><span class="sxs-lookup"><span data-stu-id="92388-280">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="92388-281">Per ulteriori informazioni, vedere [Configurazione](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="92388-281">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="92388-282">**Modello di hosting out-of-process**</span><span class="sxs-lookup"><span data-stu-id="92388-282">**Out-of-process hosting model**</span></span>

<span data-ttu-id="92388-283">Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="92388-283">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="92388-284">Nell'esempio seguente si impedisce all'app di popolare `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="92388-284">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="92388-285">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-285">Option</span></span>                         | <span data-ttu-id="92388-286">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-286">Default</span></span> | <span data-ttu-id="92388-287">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-287">Setting</span></span> |
| ---
<span data-ttu-id="92388-288">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-289">'Blazor'</span></span>
- <span data-ttu-id="92388-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-290">'Identity'</span></span>
- <span data-ttu-id="92388-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-292">'Razor'</span></span>
- <span data-ttu-id="92388-293">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-294">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-294">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-295">'Blazor'</span></span>
- <span data-ttu-id="92388-296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-296">'Identity'</span></span>
- <span data-ttu-id="92388-297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-297">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-298">'Razor'</span></span>
- <span data-ttu-id="92388-299">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-300">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-301">'Blazor'</span></span>
- <span data-ttu-id="92388-302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-302">'Identity'</span></span>
- <span data-ttu-id="92388-303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-303">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-304">'Razor'</span></span>
- <span data-ttu-id="92388-305">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-306">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-307">'Blazor'</span></span>
- <span data-ttu-id="92388-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-308">'Identity'</span></span>
- <span data-ttu-id="92388-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-310">'Razor'</span></span>
- <span data-ttu-id="92388-311">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-312">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-313">'Blazor'</span></span>
- <span data-ttu-id="92388-314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-314">'Identity'</span></span>
- <span data-ttu-id="92388-315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-315">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-316">'Razor'</span></span>
- <span data-ttu-id="92388-317">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-318">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-319">'Blazor'</span></span>
- <span data-ttu-id="92388-320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-320">'Identity'</span></span>
- <span data-ttu-id="92388-321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-321">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-322">'Razor'</span></span>
- <span data-ttu-id="92388-323">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-324">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-325">'Blazor'</span></span>
- <span data-ttu-id="92388-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-326">'Identity'</span></span>
- <span data-ttu-id="92388-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-328">'Razor'</span></span>
- <span data-ttu-id="92388-329">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-330">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-331">'Blazor'</span></span>
- <span data-ttu-id="92388-332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-332">'Identity'</span></span>
- <span data-ttu-id="92388-333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-333">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-334">'Razor'</span></span>
- <span data-ttu-id="92388-335">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-336">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-337">'Blazor'</span></span>
- <span data-ttu-id="92388-338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-338">'Identity'</span></span>
- <span data-ttu-id="92388-339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-339">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-340">'Razor'</span></span>
- <span data-ttu-id="92388-341">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-342">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-343">'Blazor'</span></span>
- <span data-ttu-id="92388-344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-344">'Identity'</span></span>
- <span data-ttu-id="92388-345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-345">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-346">'Razor'</span></span>
- <span data-ttu-id="92388-347">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-348">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-349">'Blazor'</span></span>
- <span data-ttu-id="92388-350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-350">'Identity'</span></span>
- <span data-ttu-id="92388-351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-351">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-352">'Razor'</span></span>
- <span data-ttu-id="92388-353">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-354">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-355">'Blazor'</span></span>
- <span data-ttu-id="92388-356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-356">'Identity'</span></span>
- <span data-ttu-id="92388-357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-357">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-358">'Razor'</span></span>
- <span data-ttu-id="92388-359">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-360">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-361">'Blazor'</span></span>
- <span data-ttu-id="92388-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-362">'Identity'</span></span>
- <span data-ttu-id="92388-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-364">'Razor'</span></span>
- <span data-ttu-id="92388-365">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-365">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-366">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-366">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-367">'Blazor'</span></span>
- <span data-ttu-id="92388-368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-368">'Identity'</span></span>
- <span data-ttu-id="92388-369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-369">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-370">'Razor'</span></span>
- <span data-ttu-id="92388-371">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-371">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-372">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il [middleware di integrazione di IIS](#enable-the-iisintegration-components) imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-372">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-373">Se `false`, il middleware fornisce solo un'identità per `HttpContext.User` e risponde solo alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-373">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-374">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-374">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-375">Per altre informazioni, vedere l'argomento [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-375">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="92388-376">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-376">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="92388-377">| | `ForwardClientCertificate`     | `true`  | Se `true` e l' `MS-ASPNETCORE-CLIENTCERT` intestazione della richiesta è presente, `HttpContext.Connection.ClientCertificate` viene popolato.</span><span class="sxs-lookup"><span data-stu-id="92388-377">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="92388-378">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="92388-378">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="92388-379">Il [middleware di integrazione IIS](#enable-the-iisintegration-components) e il modulo ASP.NET Core sono configurati per l'invio di:</span><span class="sxs-lookup"><span data-stu-id="92388-379">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="92388-380">Schema (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-380">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="92388-381">Indirizzo IP remoto da cui ha avuto origine la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-381">Remote IP address where the request originated.</span></span>

<span data-ttu-id="92388-382">Il [middleware di integrazione di IIS](#enable-the-iisintegration-components) configura il middleware delle intestazioni con inoltri.</span><span class="sxs-lookup"><span data-stu-id="92388-382">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="92388-383">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="92388-383">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="92388-384">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="92388-384">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="92388-385">File web.config</span><span class="sxs-lookup"><span data-stu-id="92388-385">web.config file</span></span>

<span data-ttu-id="92388-386">Il file *web.config* configura il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-386">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-387">La creazione, la trasformazione e la pubblicazione del file *web.config* sono operazioni gestite da una destinazione MSBuild (`_TransformWebConfig`) quando viene pubblicato il progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-387">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="92388-388">Questa destinazione si trova tra le destinazioni Web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="92388-388">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="92388-389">L'SDK è impostato all'inizio del file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-389">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="92388-390">Se nel progetto non è presente un file *web.config*, il file viene creato con un valore *processPath* e *argomenti* corretti per la configurazione del modulo ASP.NET Core. Il file viene quindi spostato nell'[output pubblicato](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="92388-390">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="92388-391">Se nel progetto è presente un file *web.config*, il file viene trasformato con il valore di *processPath* e gli *argomenti* corretti per la configurazione del modulo ASP.NET Core, quindi viene spostato nell'output pubblicato.</span><span class="sxs-lookup"><span data-stu-id="92388-391">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="92388-392">La trasformazione non modifica le impostazioni di configurazione di IIS incluse nel file.</span><span class="sxs-lookup"><span data-stu-id="92388-392">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="92388-393">Il file *web.config* può fornire ulteriori impostazioni di configurazione di IIS che controllano i moduli IIS attivi.</span><span class="sxs-lookup"><span data-stu-id="92388-393">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="92388-394">Per informazioni sui moduli IIS in grado di elaborare le richieste con le app di ASP.NET Core, vedere l'argomento [Moduli IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="92388-394">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="92388-395">Per impedire a Web SDK di trasformare il file *Web. config* , utilizzare la **\<IsTransformWebConfigDisabled>** proprietà nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-395">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="92388-396">Quando si disabilita la trasformazione del file in Web SDK, il valore di *processPath* e gli *argomenti* devono essere impostati manualmente dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="92388-396">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="92388-397">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-397">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="92388-398">Posizione del file web.config</span><span class="sxs-lookup"><span data-stu-id="92388-398">web.config file location</span></span>

<span data-ttu-id="92388-399">Per configurare correttamente il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , il file *Web. config* deve essere presente nel percorso [radice del contenuto](xref:fundamentals/index#content-root) (in genere il percorso di base dell'app) dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="92388-399">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="92388-400">Corrisponde al percorso fisico del sito Web fornito a IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-400">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="92388-401">Il file *web.config* deve essere presente nella radice dell'app per abilitare la pubblicazione di più app mediante Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-401">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="92388-402">I file sensibili sono presenti nel percorso fisico dell'app, ad esempio \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (commenti in formato documentazione XML) e \* \<assembly> . Deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="92388-402">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="92388-403">Quando il file *web.config* è presente e il sito viene avviato normalmente, IIS non rende disponibili questi file riservati se vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="92388-403">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="92388-404">Se il file *web.config* non è presente, ha un nome non corretto o non è in grado di configurare il sito per l'avvio normale, IIS potrebbe rendere disponibili pubblicamente i file riservati.</span><span class="sxs-lookup"><span data-stu-id="92388-404">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="92388-405">**Il file *Web. config* deve essere sempre presente nella distribuzione, denominato correttamente e in grado di configurare il sito per l'avvio normale. Non rimuovere mai il file *Web. config* da una distribuzione di produzione.**</span><span class="sxs-lookup"><span data-stu-id="92388-405">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="92388-406">Trasformare web.config</span><span class="sxs-lookup"><span data-stu-id="92388-406">Transform web.config</span></span>

<span data-ttu-id="92388-407">Se è necessario trasformare *Web. config* durante la pubblicazione, vedere <xref:host-and-deploy/iis/transform-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="92388-407">If you need to transform *web.config* on publish, see <xref:host-and-deploy/iis/transform-webconfig>.</span></span> <span data-ttu-id="92388-408">Potrebbe essere necessario trasformare *Web. config* in Publish per impostare le variabili di ambiente in base alla configurazione, al profilo o all'ambiente.</span><span class="sxs-lookup"><span data-stu-id="92388-408">You might need to transform *web.config* on publish to set environment variables based on the configuration, profile, or environment.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="92388-409">Configurazione di IIS</span><span class="sxs-lookup"><span data-stu-id="92388-409">IIS configuration</span></span>

<span data-ttu-id="92388-410">**Sistemi operativi Windows Server**</span><span class="sxs-lookup"><span data-stu-id="92388-410">**Windows Server operating systems**</span></span>

<span data-ttu-id="92388-411">Abilitare il ruolo del server **Server Web (IIS)** e stabilire i servizi di ruolo.</span><span class="sxs-lookup"><span data-stu-id="92388-411">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="92388-412">Usare la procedura guidata **Aggiungi ruoli e funzionalità** accessibile tramite il menu **Gestisci** o il collegamento in **Server Manager**.</span><span class="sxs-lookup"><span data-stu-id="92388-412">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="92388-413">Nel passaggio **Ruoli del server** selezionare la casella per **Server Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="92388-413">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Il ruolo Server Web IIS viene selezionato nel passaggio dei ruoli del server Selezionare.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="92388-415">Dopo il passaggio **Funzionalità**, viene caricato il passaggio**Servizi ruolo** per Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-415">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="92388-416">Selezionare i servizi ruolo IIS desiderati o accettare i servizi ruolo predefiniti specificati.</span><span class="sxs-lookup"><span data-stu-id="92388-416">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![I servizi ruolo predefiniti vengono selezionati nel passaggio Selezionare i servizi ruolo.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="92388-418">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-418">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-419">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: sicurezza del **server Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-419">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="92388-420">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-420">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-421">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-421">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-422">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-422">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-423">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-423">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-424">Per abilitare i WebSocket, espandere i nodi seguenti: sviluppo di applicazioni **server Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="92388-424">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="92388-425">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-425">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-426">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-426">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-427">Procedere con il passaggio **Conferma** per installare il ruolo del server web e i servizi.</span><span class="sxs-lookup"><span data-stu-id="92388-427">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="92388-428">Dopo l'installazione del ruolo **server Web (IIS)** non è necessario riavviare il server o IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-428">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="92388-429">**Sistemi operativi desktop di Windows**</span><span class="sxs-lookup"><span data-stu-id="92388-429">**Windows desktop operating systems**</span></span>

<span data-ttu-id="92388-430">Abilitare **Console di gestione IIS** e **Servizi Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-430">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-431">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Disattivare o attivare le funzionalità di Windows** (a sinistra dello schermo).</span><span class="sxs-lookup"><span data-stu-id="92388-431">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="92388-432">Aprire il nodo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="92388-432">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="92388-433">Aprire il nodo **Strumenti di gestione Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-433">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="92388-434">Selezionare la casella per **Console di gestione IIS**.</span><span class="sxs-lookup"><span data-stu-id="92388-434">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="92388-435">Selezionare la casella per **Servizi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-435">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-436">Accettare le funzionalità predefinite per **Servizi World Wide Web** o personalizzare le funzionalità IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-436">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="92388-437">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-437">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-438">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: **World Wide Web sicurezza dei servizi**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-438">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="92388-439">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-439">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-440">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-440">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-441">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-441">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-442">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-442">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-443">Per abilitare i WebSocket, espandere i nodi seguenti: funzionalità di sviluppo di applicazioni di **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="92388-443">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="92388-444">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-444">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-445">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-445">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-446">Se l'installazione di IIS richiede un riavvio, riavviare il sistema.</span><span class="sxs-lookup"><span data-stu-id="92388-446">If the IIS installation requires a restart, restart the system.</span></span>

![La console di gestione IIS e servizi World Wide Web sono selezionati nelle funzionalità di Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="92388-448">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-448">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="92388-449">Installare il *bundle di hosting .NET Core* nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-449">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="92388-450">Il bundle installa il runtime di .NET Core, la libreria di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-450">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-451">Il modulo consente l'esecuzione delle app ASP.NET Core dietro IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-451">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92388-452">Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="92388-452">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="92388-453">Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-453">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="92388-454">Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="92388-454">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="92388-455">Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="92388-455">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="92388-456">Download diretto (versione corrente)</span><span class="sxs-lookup"><span data-stu-id="92388-456">Direct download (current version)</span></span>

<span data-ttu-id="92388-457">Scaricare il programma di installazione mediante il collegamento seguente:</span><span class="sxs-lookup"><span data-stu-id="92388-457">Download the installer using the following link:</span></span>

[<span data-ttu-id="92388-458">Programma di installazione del bundle di hosting .NET Core corrente (download diretto)</span><span class="sxs-lookup"><span data-stu-id="92388-458">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="92388-459">Versioni precedenti del programma di installazione</span><span class="sxs-lookup"><span data-stu-id="92388-459">Earlier versions of the installer</span></span>

<span data-ttu-id="92388-460">Per ottenere una versione precedente del programma di installazione:</span><span class="sxs-lookup"><span data-stu-id="92388-460">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="92388-461">Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="92388-461">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="92388-462">Selezionare la versione desiderata di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-462">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="92388-463">Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="92388-463">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="92388-464">Scaricare il programma di installazione usando il collegamento **bundle di hosting** .</span><span class="sxs-lookup"><span data-stu-id="92388-464">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="92388-465">Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="92388-465">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="92388-466">Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="92388-466">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="92388-467">Installare il bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="92388-467">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="92388-468">Eseguire il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-468">Run the installer on the server.</span></span> <span data-ttu-id="92388-469">Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-469">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="92388-470">`OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-470">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="92388-471">`OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-471">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="92388-472">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-472">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-473">`OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="92388-473">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="92388-474">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-474">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-475">`OPT_NO_X86=1`: Ignora l'installazione di Runtime x86.</span><span class="sxs-lookup"><span data-stu-id="92388-475">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="92388-476">Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-476">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="92388-477">Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-477">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="92388-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa (*ApplicationHost. config*) si trova nello stesso computer dell'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-478">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="92388-479">*Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="92388-479">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="92388-480">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="92388-480">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="92388-481">Riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="92388-481">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="92388-482">Il riavvio di IIS rileva una modifica alla variabile di ambiente di sistema PATH apportata dal programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="92388-482">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="92388-483">ASP.NET Core non adotta il comportamento di rollforward per le versioni di patch dei pacchetti di Framework condivisi.</span><span class="sxs-lookup"><span data-stu-id="92388-483">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="92388-484">Dopo l'aggiornamento del Framework condiviso mediante l'installazione di un nuovo bundle di hosting, riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="92388-484">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="92388-485">Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="92388-485">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="92388-486">Installare la Distribuzione Web quando si pubblica con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-486">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="92388-487">Quando si distribuiscono app ai server con [Distribuzione Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installare la versione più recente di Distribuzione Web nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-487">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="92388-488">Per installare Distribuzione Web usare l'[Installazione guidata piattaforma Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) o ottenere direttamente un programma di installazione in [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="92388-488">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="92388-489">Il metodo preferito consiste nell'usare WebPI.</span><span class="sxs-lookup"><span data-stu-id="92388-489">The preferred method is to use WebPI.</span></span> <span data-ttu-id="92388-490">WebPI offre un'installazione autonoma e una configurazione per i provider di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-490">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="92388-491">Creare il sito IIS</span><span class="sxs-lookup"><span data-stu-id="92388-491">Create the IIS site</span></span>

1. <span data-ttu-id="92388-492">Nel sistema host creare una cartella per contenere le cartelle e i file pubblicati dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-492">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="92388-493">In un passaggio successivo, il percorso della cartella viene fornito a IIS come percorso fisico dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-493">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="92388-494">Per altre informazioni sulla cartella di distribuzione e il layout dei file di un'app, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="92388-494">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="92388-495">In Gestione IIS aprire il nodo del server nel pannello **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="92388-495">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="92388-496">Fare clic con il pulsante destro del mouse sulla cartella **Siti**.</span><span class="sxs-lookup"><span data-stu-id="92388-496">Right-click the **Sites** folder.</span></span> <span data-ttu-id="92388-497">Scegliere **Aggiungi sito Web** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-497">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-498">Specificare un **Nome del sito** e impostare il **Percorso fisico** per la cartella di distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-498">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="92388-499">Specificare la configurazione dell' **associazione** e creare il sito Web selezionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="92388-499">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Specificare il nome del sito, il percorso fisico e il nome Host nel passaggio Aggiungi sito Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="92388-501">Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate,</span><span class="sxs-lookup"><span data-stu-id="92388-501">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="92388-502">poiché possono introdurre vulnerabilità a livello di sicurezza nell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-502">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="92388-503">Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili.</span><span class="sxs-lookup"><span data-stu-id="92388-503">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="92388-504">Usare nomi host espliciti al posto di caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="92388-504">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="92388-505">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="92388-505">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="92388-506">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-506">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="92388-507">Nel nodo del server selezionare **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-507">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="92388-508">Fare clic con il pulsante destro del mouse sul pool di applicazioni del sito e scegliere **Impostazioni di base** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-508">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-509">Nella finestra **Modifica pool di applicazioni** impostare **Versione .NET CLR** su **Nessun codice gestito**.</span><span class="sxs-lookup"><span data-stu-id="92388-509">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Impostare Nessun codice gestito per la versione .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="92388-511">ASP.NET Core viene eseguito in un processo separato e gestisce il runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-511">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="92388-512">ASP.NET Core non si basa sul caricamento del CLR desktop (CLR .NET).</span><span class="sxs-lookup"><span data-stu-id="92388-512">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR).</span></span> <span data-ttu-id="92388-513">Il core Common Language Runtime (CoreCLR) per .NET Core viene avviato per ospitare l'app nel processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92388-513">The Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="92388-514">L'impostazione di **Versione .NET CLR** su **Nessun codice gestito** è facoltativa ma consigliata.</span><span class="sxs-lookup"><span data-stu-id="92388-514">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="92388-515">*ASP.NET Core 2.2 o versione successiva*:</span><span class="sxs-lookup"><span data-stu-id="92388-515">*ASP.NET Core 2.2 or later*:</span></span>

   * <span data-ttu-id="92388-516">Per una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) a 32 bit (x86) pubblicata con un SDK a 32 bit che usa il [modello di hosting in-process](#in-process-hosting-model), abilitare il Pool di applicazioni per 32 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-516">For a 32-bit (x86) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) published with a 32-bit SDK that uses the [in-process hosting model](#in-process-hosting-model), enable the Application Pool for 32-bit.</span></span> <span data-ttu-id="92388-517">In Gestione IIS passare a **pool di applicazioni** nella barra laterale **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="92388-517">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="92388-518">Selezionare il pool di applicazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-518">Select the app's Application Pool.</span></span> <span data-ttu-id="92388-519">Nella barra laterale **azioni** selezionare **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-519">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="92388-520">Impostare **Abilita applicazioni a 32 bit** su `True` .</span><span class="sxs-lookup"><span data-stu-id="92388-520">Set **Enable 32-Bit Applications** to `True`.</span></span> 

   * <span data-ttu-id="92388-521">per una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) a 64 bit (x64) che usa il [modello di hosting in-process](#in-process-hosting-model), disabilitare il pool di app per i processi a 32 bit (x86).</span><span class="sxs-lookup"><span data-stu-id="92388-521">For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span> <span data-ttu-id="92388-522">In Gestione IIS passare a **pool di applicazioni** nella barra laterale **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="92388-522">In IIS Manager, navigate to **Application Pools** in the **Connections** sidebar.</span></span> <span data-ttu-id="92388-523">Selezionare il pool di applicazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-523">Select the app's Application Pool.</span></span> <span data-ttu-id="92388-524">Nella barra laterale **azioni** selezionare **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-524">In the **Actions** sidebar, select **Advanced Settings**.</span></span> <span data-ttu-id="92388-525">Impostare **Abilita applicazioni a 32 bit** su `False` .</span><span class="sxs-lookup"><span data-stu-id="92388-525">Set **Enable 32-Bit Applications** to `False`.</span></span> 

1. <span data-ttu-id="92388-526">Confermare che l'identità del modello del processo disponga delle autorizzazioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="92388-526">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="92388-527">Se l'identità predefinita del pool di app (**modello**  >  **Identity** di processo) viene modificata da **ApplicationPoolIdentity** a un'altra identità, verificare che la nuova identità disponga delle autorizzazioni necessarie per accedere alla cartella dell'app, al database e ad altre risorse richieste.</span><span class="sxs-lookup"><span data-stu-id="92388-527">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="92388-528">Ad esempio, il pool di applicazioni richiede l'accesso in lettura e scrittura alle cartelle in cui l'app legge e scrive i file.</span><span class="sxs-lookup"><span data-stu-id="92388-528">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="92388-529">**Configurazione dell'autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-529">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="92388-530">Per altre informazioni, vedere [Configurare l'autenticazione Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-530">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="92388-531">Distribuire l'app</span><span class="sxs-lookup"><span data-stu-id="92388-531">Deploy the app</span></span>

<span data-ttu-id="92388-532">Distribuire l'app nella cartella **Percorso fisico** di IIS specificata nella sezione [Creare il sito IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="92388-532">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="92388-533">[Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) è il meccanismo consigliato per la distribuzione, ma esistono diverse opzioni per spostare l'app dalla cartella di *pubblicazione* del progetto alla cartella di distribuzione del sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-533">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="92388-534">Distribuzione web con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-534">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="92388-535">Vedere l'argomento [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Profili di pubblicazione Visual Studio per la distribuzione di app ASP.NET Core) per informazioni su come creare un profilo di pubblicazione da usare con Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-535">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="92388-536">Se il provider di hosting fornisce un profilo di pubblicazione o il supporto per crearne uno, scaricare il profilo e importarlo usando la finestra di dialogo **Pubblica** di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="92388-536">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Pagina della finestra di dialogo Pubblica](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="92388-538">Distribuzione Web all'esterno di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-538">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="92388-539">È anche possibile usare [Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) all'esterno di Visual Studio dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="92388-539">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="92388-540">Per altre informazioni sulla distribuzione, vedere [Strumento Distribuzione Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="92388-540">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="92388-541">Alternative a Distribuzione web</span><span class="sxs-lookup"><span data-stu-id="92388-541">Alternatives to Web Deploy</span></span>

<span data-ttu-id="92388-542">Usare uno dei metodi disponibili, ad esempio la copia manuale, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/), per spostare l'app nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-542">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="92388-543">Per altre informazioni sulla distribuzione di ASP.NET Core in IIS, vedere la sezione [Risorse di distribuzione per amministratori IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="92388-543">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="92388-544">Esplorare il sito Web</span><span class="sxs-lookup"><span data-stu-id="92388-544">Browse the website</span></span>

<span data-ttu-id="92388-545">Dopo aver distribuito l'app nel sistema di hosting, effettuare una richiesta a uno degli endpoint pubblici dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-545">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="92388-546">Nell'esempio seguente il sito è associato al **nome host** di IIS `www.mysite.com` sulla **porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="92388-546">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="92388-547">Viene effettuata una richiesta a `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="92388-547">A request is made to `http://www.mysite.com`:</span></span>

![Il browser Microsoft Edge ha caricato la pagina di avvio IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="92388-549">File di distribuzione bloccati</span><span class="sxs-lookup"><span data-stu-id="92388-549">Locked deployment files</span></span>

<span data-ttu-id="92388-550">I file nella cartella di distribuzione sono bloccati durante l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-550">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="92388-551">I file bloccati non possono essere sovrascritti durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-551">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="92388-552">Per rilasciare i file bloccati in una distribuzione, arrestare il pool di applicazioni usando **uno** dei metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-552">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-553">Usare Distribuzione Web e fare riferimento a `Microsoft.NET.Sdk.Web` nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-553">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="92388-554">Nella radice della directory dell'app web viene posizionato un file *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="92388-554">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="92388-555">Quando il file è presente, il modulo ASP.NET Core arresta normalmente l'app e rende disponibile il file *app_offline.htm* durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-555">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="92388-556">Per altre informazioni, vedere la [Guida di riferimento per la configurazione del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="92388-556">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="92388-557">Arrestare manualmente il pool di applicazioni in Gestione IIS sul server.</span><span class="sxs-lookup"><span data-stu-id="92388-557">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="92388-558">Usare PowerShell per eliminare *App_offline. htm* (richiede PowerShell 5 o versione successiva):</span><span class="sxs-lookup"><span data-stu-id="92388-558">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="92388-559">Protezione dei dati</span><span class="sxs-lookup"><span data-stu-id="92388-559">Data protection</span></span>

<span data-ttu-id="92388-560">Lo [stack di protezione dei dati di ASP.NET Core](xref:security/data-protection/introduction) viene usato da diversi [middleware](xref:fundamentals/middleware/index) di ASP.NET Core, inclusi quelli usati nell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="92388-560">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="92388-561">Anche se le DPAPI (Data Protection API) non vengono chiamate dal codice dell'utente, è consigliabile configurare la protezione dati per la creazione di un [archivio di chiavi](xref:security/data-protection/implementation/key-management) crittografiche permanente con uno script di distribuzione o nel codice dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-561">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="92388-562">Se non si configura la protezione dei dati, le chiavi vengono mantenute in memoria ed eliminate al riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-562">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="92388-563">Se il gruppo di chiavi viene archiviato in memoria quando l'app viene riavviata:</span><span class="sxs-lookup"><span data-stu-id="92388-563">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="92388-564">Tutti i token di autenticazione basati su cookie vengono invalidati.</span><span class="sxs-lookup"><span data-stu-id="92388-564">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="92388-565">Gli utenti devono ripetere l'accesso alla richiesta successiva.</span><span class="sxs-lookup"><span data-stu-id="92388-565">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="92388-566">Tutti i dati protetti con il gruppo di chiavi non possono più essere decrittografati.</span><span class="sxs-lookup"><span data-stu-id="92388-566">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="92388-567">Questo può includere i [token CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e i [cookie TEMPDATA di ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="92388-567">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="92388-568">Per configurare la protezione dati in IIS in modo da rendere permanente il gruppo di chiavi, usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-568">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-569">**Creare chiavi del Registro di sistema di protezione dati**</span><span class="sxs-lookup"><span data-stu-id="92388-569">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="92388-570">Le chiavi di protezione dati usate dalle app ASP.NET Core vengono archiviate nel Registro di sistema esterno alle app.</span><span class="sxs-lookup"><span data-stu-id="92388-570">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="92388-571">Per mantenere le chiavi per una determinata app, creare chiavi del Registro di sistema per il pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-571">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="92388-572">Per le installazioni IIS autonome non web farm è possibile usare lo [script PowerShell Data Protection Provision-AutoGenKeys.ps1 (Provisioning di protezione dati-AutoGenKeys.ps1)](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) per ogni pool di app usato con un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-572">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="92388-573">Questo script crea una chiave del Registro di sistema nel registro HKLM accessibile solo all'account del processo di lavoro del pool di applicazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-573">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="92388-574">Le chiavi vengono crittografate a riposo tramite DPAPI con una chiave a livello del computer.</span><span class="sxs-lookup"><span data-stu-id="92388-574">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="92388-575">In scenari di web farm un'app può essere configurata in modo da usare un percorso UNC in cui archiviare il proprio gruppo di chiavi di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="92388-575">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="92388-576">Per impostazione predefinita, le chiavi di protezione dati non vengono crittografate.</span><span class="sxs-lookup"><span data-stu-id="92388-576">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="92388-577">Assicurarsi che le autorizzazioni file per la condivisione di rete siano limitate all'account di Windows in cui viene eseguita l'app.</span><span class="sxs-lookup"><span data-stu-id="92388-577">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="92388-578">È possibile usare un certificato X509 per la protezione delle chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="92388-578">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="92388-579">Considerare l'implementazione di un meccanismo per consentire agli utenti di caricare i certificati: inserire i certificati nell'archivio certificati attendibili dell'utente e assicurarsi che siano disponibili in tutti i computer in cui viene eseguita l'app dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-579">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="92388-580">Vedere [Configurare la protezione dati di ASP.NET Core](xref:security/data-protection/configuration/overview) per altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="92388-580">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="92388-581">**Configurare il pool di applicazioni IIS per caricare il profilo utente**</span><span class="sxs-lookup"><span data-stu-id="92388-581">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="92388-582">Questa impostazione si trova nella sezione **Modello del processo** in **Impostazioni avanzate** per il pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-582">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="92388-583">Impostare **Caricamento del profilo utente** su `True`.</span><span class="sxs-lookup"><span data-stu-id="92388-583">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="92388-584">Con l'impostazione `True` le chiavi vengono archiviate nella directory del profilo utente e protette tramite DPAPI con una chiave specifica per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="92388-584">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="92388-585">Le chiavi vengono salvate in modo permanente nella cartella *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="92388-585">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="92388-586">Deve essere abilitato anche l'[attributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-586">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="92388-587">Il valore predefinito di `setProfileEnvironment` è `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-587">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="92388-588">In alcuni scenari (ad esempio, per il sistema operativo Windows), `setProfileEnvironment` è impostato su `false`.</span><span class="sxs-lookup"><span data-stu-id="92388-588">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="92388-589">Se le chiavi non vengono archiviate nella directory del profilo utente come previsto:</span><span class="sxs-lookup"><span data-stu-id="92388-589">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="92388-590">Passare alla cartella *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="92388-590">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="92388-591">Aprire il file *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="92388-591">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="92388-592">Individuare l'elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="92388-592">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="92388-593">Verificare che l'attributo `setProfileEnvironment` non sia presente, condizione che corrisponde all'impostazione predefinita `true`, o impostare in modo esplicito il valore dell'attributo su `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-593">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="92388-594">**Usare il file system come archivio del gruppo di chiavi**</span><span class="sxs-lookup"><span data-stu-id="92388-594">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="92388-595">Modificare il codice dell'app per [usare il file system come archivio del gruppo di chiavi](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-595">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="92388-596">Usare un certificato X509 per proteggere il gruppo di chiavi e verificare che sia un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-596">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="92388-597">Se il certificato è autofirmato, inserirlo nell'archivio radice attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-597">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="92388-598">Quando si usa IIS in una web farm:</span><span class="sxs-lookup"><span data-stu-id="92388-598">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="92388-599">Usare una condivisione di file a cui possono accedere tutti i computer.</span><span class="sxs-lookup"><span data-stu-id="92388-599">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="92388-600">Distribuire un certificato X509 in ogni computer.</span><span class="sxs-lookup"><span data-stu-id="92388-600">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="92388-601">Configurare [protezione dei dati nel codice](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-601">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="92388-602">**Impostare criteri a livello di computer per la protezione dei dati**</span><span class="sxs-lookup"><span data-stu-id="92388-602">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="92388-603">Il sistema di protezione dei dati ha un supporto limitato per l'impostazione di [criteri a livello di computer](xref:security/data-protection/configuration/machine-wide-policy) predefiniti per tutte le app che usano le API di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="92388-603">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="92388-604">Per altre informazioni, vedere <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92388-604">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="92388-605">Directory virtuali</span><span class="sxs-lookup"><span data-stu-id="92388-605">Virtual Directories</span></span>

<span data-ttu-id="92388-606">Le [directory virtuali IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) non sono supportate con le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-606">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="92388-607">Un'app può essere ospitata come [applicazione secondaria](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="92388-607">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="92388-608">Applicazioni secondarie</span><span class="sxs-lookup"><span data-stu-id="92388-608">Sub-applications</span></span>

<span data-ttu-id="92388-609">Un'app ASP.NET Core può essere ospitata come [applicazione secondaria di IIS (app secondaria)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="92388-609">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="92388-610">Il percorso dell'app secondaria diventa parte dell'URL dell'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-610">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="92388-611">I collegamenti ad asset statici all'interno dell'app secondaria devono usare la notazione con tilde-barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="92388-611">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="92388-612">La notazione con tilde-barra attiva un [helper tag](xref:mvc/views/tag-helpers/intro) per anteporre la base del percorso dell'app secondaria al collegamento relativo sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="92388-612">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="92388-613">Per un'app secondaria in `/subapp_path`, il rendering di un'immagine collegata con `src="~/image.png"` viene eseguito come `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="92388-613">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="92388-614">Il middleware dei file statici dell'app radice non elabora la richiesta di file statici.</span><span class="sxs-lookup"><span data-stu-id="92388-614">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="92388-615">La richiesta viene elaborata dal middleware dei file statici dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-615">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="92388-616">Se l'attributo `src` di un asset statico viene impostato su un percorso assoluto (ad esempio, `src="/image.png"`), il rendering del collegamento viene eseguito senza la base del percorso dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-616">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="92388-617">Il middleware dei file statici dell'app radice prova a servire l'asset dalla [radice Web](xref:fundamentals/index#web-root) dell'app radice con conseguente risposta *404 - Non trovato*, a meno che l'asset statico non sia disponibile dal'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-617">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="92388-618">Per ospitare un'app ASP.NET Core come app secondaria in un'altra app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="92388-618">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="92388-619">Definire un pool di app per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-619">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="92388-620">Impostare **Versione .NET CLR** su **Nessun codice gestito** perché Core Common Language Runtime (CoreCLR) for .NET Core viene avviato per ospitare l'app nel processo di lavoro e non CLR desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="92388-620">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="92388-621">Aggiungere il sito radice in Gestione IIS con l'applicazione secondaria in una cartella all'interno del sito principale.</span><span class="sxs-lookup"><span data-stu-id="92388-621">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="92388-622">Fare clic con il pulsante destro del mouse sulla cartella dell'applicazione secondaria in Gestione IIS e scegliere **Converti in applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-622">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="92388-623">Nella finestra di dialogo **Aggiungi applicazione** usare il pulsante **Seleziona** per **Pool di applicazioni** per assegnare il pool di app creato per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-623">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="92388-624">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-624">Select **OK**.</span></span>

<span data-ttu-id="92388-625">L'assegnazione di un pool di app separato all'app secondaria è un requisito quando si usa il modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="92388-625">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="92388-626">Per ulteriori informazioni sul modello di hosting in-process e sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="92388-626">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="92388-627">Configurazione di IIS con web.config</span><span class="sxs-lookup"><span data-stu-id="92388-627">Configuration of IIS with web.config</span></span>

<span data-ttu-id="92388-628">La configurazione di IIS è influenzata dalla sezione `<system.webServer>` di *web.config* per gli scenari IIS funzionali per le app ASP.NET Core con il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-628">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="92388-629">Ad esempio, la configurazione di IIS è funzionale per la compressione dinamica.</span><span class="sxs-lookup"><span data-stu-id="92388-629">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="92388-630">Se IIS è configurato a livello di server per l'uso della compressione dinamica, l'elemento `<urlCompression>` nel file *web.config* dell'app può disabilitare questa impostazione per un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-630">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="92388-631">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-631">For more information, see the following topics:</span></span>

* [<span data-ttu-id="92388-632">Riferimento alla configurazione per\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="92388-632">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="92388-633">Per impostare le variabili di ambiente per singole app in esecuzione in pool di applicazioni isolate (supportate per IIS 10,0 o versioni successive), vedere la sezione *comando appcmd. exe* dell'argomento [ \<environmentVariables> variabili di ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) nella documentazione di riferimento di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-633">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="92388-634">Sezioni di configurazione di web.config</span><span class="sxs-lookup"><span data-stu-id="92388-634">Configuration sections of web.config</span></span>

<span data-ttu-id="92388-635">Le sezioni di configurazione di app ASP.NET 4.x in *web.config* non vengono usate dalle app ASP.NET Core per la configurazione:</span><span class="sxs-lookup"><span data-stu-id="92388-635">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="92388-636">Le applicazioni ASP.NET Core vengono configurate tramite altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="92388-636">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="92388-637">Per ulteriori informazioni, vedere [configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="92388-637">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="92388-638">Pool di applicazioni</span><span class="sxs-lookup"><span data-stu-id="92388-638">Application Pools</span></span>

<span data-ttu-id="92388-639">L'isolamento dei pool di app è determinato dal modello di hosting:</span><span class="sxs-lookup"><span data-stu-id="92388-639">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="92388-640">Hosting in-process: le app sono necessarie per l'esecuzione in pool di applicazioni separati.</span><span class="sxs-lookup"><span data-stu-id="92388-640">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="92388-641">Hosting out-of-process: è consigliabile isolare le app le une dalle altre eseguendo ogni app nel proprio pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-641">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="92388-642">Nella finestra di dialogo **Aggiungi sito Web** è selezionato per impostazione predefinita un singolo pool di app per ogni app.</span><span class="sxs-lookup"><span data-stu-id="92388-642">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="92388-643">Quando si specifica un valore in **Nome del sito**, il testo viene automaticamente trasferito alla casella di testo **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-643">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="92388-644">Quando si aggiunge il sito viene creato un nuovo pool di applicazioni con il nome del sito.</span><span class="sxs-lookup"><span data-stu-id="92388-644">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="92388-645">Pool di applicazioniIdentity</span><span class="sxs-lookup"><span data-stu-id="92388-645">Application Pool Identity</span></span>

<span data-ttu-id="92388-646">Un account di identità del pool di app consente di eseguire un'app in un account univoco, senza dover creare e gestire domini o account locali.</span><span class="sxs-lookup"><span data-stu-id="92388-646">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="92388-647">In IIS 8.0 o versioni successive il processo di lavoro amministrazione IIS (WAS) crea un account virtuale con il nome del nuovo pool di applicazioni ed esegue i processi di lavoro del pool di applicazioni inclusi nell'account per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-647">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="92388-648">Nella console di gestione IIS in **Impostazioni avanzate** per il pool di applicazioni, verificare che **Identity** sia impostato per utilizzare **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="92388-648">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Finestra di dialogo Impostazioni avanzate del pool di applicazione](index/_static/apppool-identity.png)

<span data-ttu-id="92388-650">Il processo di gestione IIS crea un identificatore sicuro con il nome del pool di app nel sistema di sicurezza di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-650">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="92388-651">Le risorse possono essere protette mediante questa identità,</span><span class="sxs-lookup"><span data-stu-id="92388-651">Resources can be secured using this identity.</span></span> <span data-ttu-id="92388-652">che tuttavia non è un account utente reale e non viene visualizzata nella console di gestione utenti di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-652">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="92388-653">Se il processo di lavoro IIS richiede l'accesso con privilegi elevati all'app, modificare l'elenco di controllo di accesso (ACL) della directory contenente l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-653">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="92388-654">Aprire Esplora risorse, quindi spostarsi nella directory.</span><span class="sxs-lookup"><span data-stu-id="92388-654">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="92388-655">Fare clic con il pulsante destro del mouse sulla directory e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="92388-655">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="92388-656">Nella scheda **Sicurezza** selezionare il pulsante **Modifica** e quindi il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="92388-656">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="92388-657">Fare clic sul pulsante **Percorsi** e verificare che il sistema sia selezionato.</span><span class="sxs-lookup"><span data-stu-id="92388-657">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="92388-658">Immettere **IIS AppPool\\<nome_pool_applicazioni>** nell'area **Immettere i nomi degli oggetti da selezionare**.</span><span class="sxs-lookup"><span data-stu-id="92388-658">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="92388-659">Fare clic sul pulsante **Controlla nomi**.</span><span class="sxs-lookup"><span data-stu-id="92388-659">Select the **Check Names** button.</span></span> <span data-ttu-id="92388-660">Per *DefaultAppPool* controllare i nomi usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-660">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="92388-661">Quando il pulsante **Controlla nomi** è selezionato, nell'area dei nomi degli oggetti viene indicato un valore di **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-661">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="92388-662">Non è possibile immettere il nome del pool di applicazioni direttamente nell'area dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-662">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="92388-663">Usare il formato **IIS AppPool\\<nome_pool_applicazioni>** durante il controllo dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-663">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: il nome del pool di applicazioni "DefaultAppPool" viene aggiunto in coda a "IIS AppPool\" nell'area dei nomi degli oggetti prima di selezionare "Controlla nomi".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="92388-665">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-665">Select **OK**.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: dopo aver selezionato "Controlla nomi", il nome dell'oggetto "DefaultAppPool" viene visualizzato nell'area dei nomi degli oggetti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="92388-667">Le autorizzazioni di lettura ed esecuzione devono essere concesse per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-667">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="92388-668">Fornire autorizzazioni aggiuntive in base alle necessità.</span><span class="sxs-lookup"><span data-stu-id="92388-668">Provide additional permissions as needed.</span></span>

<span data-ttu-id="92388-669">L'accesso può essere concesso anche dal prompt dei comandi usando lo strumento **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="92388-669">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="92388-670">Usando *DefaultAppPool* come esempio, viene eseguito il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="92388-670">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="92388-671">Per altre informazioni, vedere l'argomento [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="92388-671">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="92388-672">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="92388-672">HTTP/2 support</span></span>

<span data-ttu-id="92388-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato con ASP.NET Core negli scenari di distribuzione IIS seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-673">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="92388-674">In-Process</span><span class="sxs-lookup"><span data-stu-id="92388-674">In-process</span></span>
  * <span data-ttu-id="92388-675">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="92388-675">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92388-676">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="92388-676">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="92388-677">Out-of-process</span><span class="sxs-lookup"><span data-stu-id="92388-677">Out-of-process</span></span>
  * <span data-ttu-id="92388-678">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="92388-678">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92388-679">Le connessioni server perimetrali pubbliche usano HTTP/2, mentre la connessione con proxy inverso al [server Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-679">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="92388-680">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="92388-680">TLS 1.2 or later connection</span></span>

<span data-ttu-id="92388-681">Per una distribuzione In-Process, se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="92388-681">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="92388-682">Per una distribuzione out-of-process, se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="92388-682">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="92388-683">Per altre informazioni sui modelli di hosting in-process e out-of-process, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-683">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="92388-684">HTTP/2 è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="92388-685">Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-685">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="92388-686">Per altre informazioni sulla configurazione di HTTP/2 con le distribuzioni IIS, vedere [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-686">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="92388-687">Richieste preliminari CORS</span><span class="sxs-lookup"><span data-stu-id="92388-687">CORS preflight requests</span></span>

<span data-ttu-id="92388-688">*Questa sezione si applica solo alle app ASP.NET Core destinate a .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="92388-688">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="92388-689">Per un'app ASP.NET Core destinata a .NET Framework, le richieste OPTIONS non vengono passate all'app per impostazione predefinita in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-689">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="92388-690">Per informazioni su come configurare i gestori IIS dell'app in *Web. config* per passare le richieste di opzioni, vedere [abilitare le richieste tra le origini in API Web ASP.NET 2:](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)funzionamento di CORS.</span><span class="sxs-lookup"><span data-stu-id="92388-690">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="92388-691">Modulo di inizializzazione dell'applicazione e timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-691">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="92388-692">Per l'hosting in IIS dal modulo ASP.NET Core versione 2:</span><span class="sxs-lookup"><span data-stu-id="92388-692">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="92388-693">[Modulo di inizializzazione dell'applicazione](#application-initialization-module): l'app è ospitata [in-process](#in-process-hosting-model) o [out-of-process](#out-of-process-hosting-model) può essere configurata per l'avvio automatico in un processo di lavoro o il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="92388-693">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="92388-694">[Timeout di inattività](#idle-timeout): le app ospitate [in-process](#in-process-hosting-model) possono essere configurate in modo da non eseguire il timeout durante i periodi di inattività.</span><span class="sxs-lookup"><span data-stu-id="92388-694">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="92388-695">Modulo Inizializzazione applicazione</span><span class="sxs-lookup"><span data-stu-id="92388-695">Application Initialization Module</span></span>

<span data-ttu-id="92388-696">*Si applica alle app ospitate in-process e out-of-process.*</span><span class="sxs-lookup"><span data-stu-id="92388-696">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="92388-697">[Inizializzazione applicazione di IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) è una funzionalità di IIS che invia una richiesta HTTP all'app quando il pool di app viene avviato o riciclato.</span><span class="sxs-lookup"><span data-stu-id="92388-697">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="92388-698">La richiesta attiva l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-698">The request triggers the app to start.</span></span> <span data-ttu-id="92388-699">Per impostazione predefinita, IIS invia una richiesta all'URL radice dell'app (`/`) per inizializzare l'app (vedere le [risorse aggiuntive](#application-initialization-module-and-idle-timeout-additional-resources) per altri dettagli sulla configurazione).</span><span class="sxs-lookup"><span data-stu-id="92388-699">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="92388-700">Verificare che la funzionalità del ruolo Inizializzazione applicazione di IIS sia abilitata:</span><span class="sxs-lookup"><span data-stu-id="92388-700">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="92388-701">Nei sistemi desktop Windows 7 o versioni successive quando si usa IIS in locale:</span><span class="sxs-lookup"><span data-stu-id="92388-701">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="92388-702">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Disattivare o attivare le funzionalità di Windows** (a sinistra dello schermo).</span><span class="sxs-lookup"><span data-stu-id="92388-702">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="92388-703">Aprire **Internet Information Services** > **Servizi Web** > **Funzionalità per lo sviluppo di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-703">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="92388-704">Selezionare la casella di controllo **Inizializzazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-704">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="92388-705">In Windows Server 2008 R2 o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="92388-705">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="92388-706">Aprire l'**Aggiunta guidata ruoli e funzionalità**.</span><span class="sxs-lookup"><span data-stu-id="92388-706">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="92388-707">Nel pannello **Selezione servizi ruolo** aprire il nodo **Sviluppo applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-707">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="92388-708">Selezionare la casella di controllo **Inizializzazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-708">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="92388-709">Per abilitare il modulo Inizializzazione applicazione per il sito, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-709">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="92388-710">In Gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-710">Using IIS Manager:</span></span>

  1. <span data-ttu-id="92388-711">Selezionare **Pool di applicazioni** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-711">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="92388-712">Fare clic con il pulsante destro del mouse sul pool di app dell'app nell'elenco e scegliere **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-712">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="92388-713">L'impostazione predefinita per **Modalità avvio** è **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="92388-713">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="92388-714">Impostare **Modalità avvio** su **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="92388-714">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="92388-715">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-715">Select **OK**.</span></span>
  1. <span data-ttu-id="92388-716">Aprire il nodo **Siti** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-716">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="92388-717">Fare clic con il pulsante destro del mouse sull'app e scegliere **Gestisci sito Web** > **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-717">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="92388-718">L'impostazione predefinita di **Precaricamento abilitato** è **False**.</span><span class="sxs-lookup"><span data-stu-id="92388-718">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="92388-719">Impostare **Precaricamento abilitato** su **True**.</span><span class="sxs-lookup"><span data-stu-id="92388-719">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="92388-720">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-720">Select **OK**.</span></span>

* <span data-ttu-id="92388-721">Usando *web.config* aggiungere l'elemento `<applicationInitialization>` con `doAppInitAfterRestart` impostato su `true` per gli elementi `<system.webServer>` nel file *web.config* dell'app:</span><span class="sxs-lookup"><span data-stu-id="92388-721">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="92388-722">Timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-722">Idle Timeout</span></span>

<span data-ttu-id="92388-723">*Si applica solo alle app ospitate in-process.*</span><span class="sxs-lookup"><span data-stu-id="92388-723">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="92388-724">Per evitare l'inattività dell'app, impostare il timeout di inattività del pool di app tramite Gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-724">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="92388-725">Selezionare **Pool di applicazioni** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-725">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="92388-726">Fare clic con il pulsante destro del mouse sul pool di app dell'app nell'elenco e scegliere **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-726">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="92388-727">L'impostazione predefinita di **Timeout di inattività (minuti)** è **20** minuti.</span><span class="sxs-lookup"><span data-stu-id="92388-727">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="92388-728">Impostare **Timeout di inattività (minuti)** su **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="92388-728">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="92388-729">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-729">Select **OK**.</span></span>
1. <span data-ttu-id="92388-730">Riciclare il processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92388-730">Recycle the worker process.</span></span>

<span data-ttu-id="92388-731">Per evitare il timeout delle app ospitate [out-of-process](#out-of-process-hosting-model), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-731">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="92388-732">Effettuare il ping dell'app da un servizio esterno per mantenerla in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-732">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="92388-733">Se l'app ospita solo servizi in background, evitare l'hosting IIS e usare un [servizio Windows per ospitare l'app ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="92388-733">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="92388-734">Risorse aggiuntive per il modulo Inizializzazione applicazione e il timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-734">Application Initialization Module and Idle Timeout additional resources</span></span>

* <span data-ttu-id="92388-735">[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) (Inizializzazione delle applicazioni in IIS 8.0)</span><span class="sxs-lookup"><span data-stu-id="92388-735">[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)</span></span>
* <span data-ttu-id="92388-736">[Inizializzazione \<applicationInitialization> dell'applicazione ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="92388-736">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="92388-737">[Impostazioni del modello di processo per un \<processModel> pool di applicazioni ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="92388-737">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="92388-738">Risorse di distribuzione per amministratori IIS</span><span class="sxs-lookup"><span data-stu-id="92388-738">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="92388-739">Documentazione di ISS</span><span class="sxs-lookup"><span data-stu-id="92388-739">IIS documentation</span></span>](/iis)
* <span data-ttu-id="92388-740">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-740">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* [<span data-ttu-id="92388-741">Distribuzione di applicazioni .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-741">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="92388-742">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="92388-742">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="92388-743">Il sito IIS ufficiale di Microsoft</span><span class="sxs-lookup"><span data-stu-id="92388-743">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="92388-744">Libreria di contenuti tecnici di Windows Server</span><span class="sxs-lookup"><span data-stu-id="92388-744">Windows Server technical content library</span></span>](/windows-server/windows-server)
* <span data-ttu-id="92388-745">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-745">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)</span></span>
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="92388-746">Per un'esercitazione sulla pubblicazione di un'app ASP.NET Core in un server IIS, vedere <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="92388-746">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="92388-747">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-747">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="92388-748">Sistemi operativi supportati</span><span class="sxs-lookup"><span data-stu-id="92388-748">Supported operating systems</span></span>

<span data-ttu-id="92388-749">Sono supportati i sistemi operativi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-749">The following operating systems are supported:</span></span>

* <span data-ttu-id="92388-750">Windows 7 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-750">Windows 7 or later</span></span>
* <span data-ttu-id="92388-751">Windows Server 2008 R2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-751">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="92388-752">Il [server HTTP.sys](xref:fundamentals/servers/httpsys) (chiamato in precedenza WebListener) non funziona in una configurazione proxy inverso con IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-752">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="92388-753">È necessario usare il [server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-753">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92388-754">Per informazioni sull'hosting in Azure, vedere <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="92388-754">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="92388-755">Per linee guida per la risoluzione dei problemi, vedere <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="92388-755">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="92388-756">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="92388-756">Supported platforms</span></span>

<span data-ttu-id="92388-757">Sono supportate le app pubblicate per la distribuzione a 32 bit (x86) o a 64 bit (x64).</span><span class="sxs-lookup"><span data-stu-id="92388-757">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="92388-758">Distribuire un'app a 32 bit con .NET Core SDK a 32 bit (x86), a meno che l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-758">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="92388-759">Non richieda lo spazio indirizzi di memoria virtuale più grande disponibile per un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-759">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="92388-760">Non richieda le dimensioni maggiori dello stack IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-760">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="92388-761">Non abbia dipendenze native a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-761">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="92388-762">Usare .NET Core SDK a 64 bit (x64) per pubblicare un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-762">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="92388-763">Un runtime a 64 bit deve essere presente nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="92388-763">A 64-bit runtime must be present on the host system.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="92388-764">Modelli di hosting</span><span class="sxs-lookup"><span data-stu-id="92388-764">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="92388-765">Modello di hosting in-process</span><span class="sxs-lookup"><span data-stu-id="92388-765">In-process hosting model</span></span>

<span data-ttu-id="92388-766">Se si usa l'hosting in-process, un'app ASP.NET Core esegue lo stesso processo del processo di lavoro IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-766">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="92388-767">L'hosting in-process offre prestazioni migliori rispetto all'hosting out-of-process perché:</span><span class="sxs-lookup"><span data-stu-id="92388-767">In-process hosting provides improved performance over out-of-process hosting because:</span></span>

* <span data-ttu-id="92388-768">Le richieste non vengono inoltrate tramite proxy sulla scheda loopback.</span><span class="sxs-lookup"><span data-stu-id="92388-768">Requests aren't proxied over the loopback adapter.</span></span> <span data-ttu-id="92388-769">Una scheda di loopback è un'interfaccia di rete che restituisce il traffico di rete in uscita allo stesso computer.</span><span class="sxs-lookup"><span data-stu-id="92388-769">A loopback adapter is a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="92388-770">Per gestire il processo, IIS usa il [servizio Attivazione processo Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="92388-770">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="92388-771">Il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="92388-771">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="92388-772">Esegue l'inizializzazione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-772">Performs app initialization.</span></span>
  * <span data-ttu-id="92388-773">Carica [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="92388-773">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="92388-774">Chiama `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="92388-774">Calls `Program.Main`.</span></span>
* <span data-ttu-id="92388-775">Gestisce la durata della richiesta nativa di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-775">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="92388-776">Il modello di hosting In-Process non è supportato per le app ASP.NET Core destinate a .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="92388-776">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="92388-777">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata in-process:</span><span class="sxs-lookup"><span data-stu-id="92388-777">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting in-process](index/_static/ancm-inprocess.png)

<span data-ttu-id="92388-779">Una richiesta arriva dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="92388-779">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="92388-780">Il driver instrada la richiesta nativa IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-780">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="92388-781">Il modulo ASP.NET Core riceve la richiesta nativa e la passa al server HTTP IIS ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="92388-781">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="92388-782">Il server HTTP di IIS è un'implementazione di server in-process per IIS che converte la richiesta da nativa a gestita.</span><span class="sxs-lookup"><span data-stu-id="92388-782">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="92388-783">Dopo che la richiesta è stata elaborata dal server HTTP di IIS, viene eseguito il push della richiesta nella pipeline middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-783">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="92388-784">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-784">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="92388-785">La risposta dell'app viene restituita a IIS tramite il server HTTP di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-785">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="92388-786">IIS invia la risposta al client che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-786">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="92388-787">L'hosting in-process è una funzionalità che richiede il consenso esplicito per le app esistenti, mentre i modelli [dotnet new](/dotnet/core/tools/dotnet-new) usano per impostazione predefinita il modello di hosting in-process per tutti gli scenari IIS e IIS Express.</span><span class="sxs-lookup"><span data-stu-id="92388-787">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="92388-788">`CreateDefaultBuilder` aggiunge un'istanza di <xref:Microsoft.AspNetCore.Hosting.Server.IServer> chiamando il metodo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> per avviare [CoreCLR](/dotnet/standard/glossary#coreclr) e ospitare l'app all'interno del processo di lavoro IIS (*w3wp.exe* o *iisexpress.exe*).</span><span class="sxs-lookup"><span data-stu-id="92388-788">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="92388-789">I test delle prestazioni indicano che l'hosting di un'app .NET Core in-process offre una velocità effettiva delle richieste significativamente superiore rispetto all'hosting dell'app out-of-process o all'inoltro delle richieste al server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-789">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="92388-790">Modello di hosting out-of-process</span><span class="sxs-lookup"><span data-stu-id="92388-790">Out-of-process hosting model</span></span>

<span data-ttu-id="92388-791">Poiché le app ASP.NET Core vengono eseguite in un processo separato dal processo di lavoro IIS, il modulo ASP.NET Core gestisce la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="92388-791">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="92388-792">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="92388-792">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="92388-793">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="92388-793">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="92388-794">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:</span><span class="sxs-lookup"><span data-stu-id="92388-794">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modulo ASP.NET Core nello scenario di hosting out-of-process](index/_static/ancm-outofprocess.png)

<span data-ttu-id="92388-796">Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="92388-796">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="92388-797">Il driver instrada le richieste a IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-797">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="92388-798">Il modulo inoltra le richieste a Kestrel su una porta casuale per l'app non corrispondente alla porta 80 o 443.</span><span class="sxs-lookup"><span data-stu-id="92388-798">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="92388-799">Il modulo specifica la porta tramite una variabile di ambiente all'avvio e l'estensione <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configura il server per l'ascolto su `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="92388-799">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="92388-800">Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate.</span><span class="sxs-lookup"><span data-stu-id="92388-800">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="92388-801">Il modulo non supporta l'inoltro HTTPS, pertanto le richieste vengono inoltrate tramite HTTP anche se sono state ricevute da IIS tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="92388-801">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="92388-802">Dopo che Kestrel ha prelevato la richiesta dal modulo, viene eseguito il push della richiesta nella pipeline middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-802">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="92388-803">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-803">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="92388-804">Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="92388-804">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="92388-805">La risposta dell'app viene quindi passata a IIS, che ne esegue di nuovo il push al client HTTP che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-805">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="92388-806">Per indicazioni sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-806">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="92388-807">Per altre informazioni sull'hosting, vedere [Hosting in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="92388-807">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="92388-808">Configurazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="92388-808">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="92388-809">Abilitare i componenti IISIntegration</span><span class="sxs-lookup"><span data-stu-id="92388-809">Enable the IISIntegration components</span></span>

<span data-ttu-id="92388-810">Quando si compila un host in `CreateWebHostBuilder` (*Program.cs*), chiamare <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> per abilitare l'integrazione con IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-810">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="92388-811">Per altre informazioni su `CreateDefaultBuilder`, vedere <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="92388-811">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="92388-812">Opzioni IIS</span><span class="sxs-lookup"><span data-stu-id="92388-812">IIS options</span></span>

<span data-ttu-id="92388-813">**Modello di hosting in-process**</span><span class="sxs-lookup"><span data-stu-id="92388-813">**In-process hosting model**</span></span>

<span data-ttu-id="92388-814">Per configurare le opzioni del server IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="92388-814">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="92388-815">L'esempio seguente disabilita AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="92388-815">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="92388-816">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-816">Option</span></span>                         | <span data-ttu-id="92388-817">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-817">Default</span></span> | <span data-ttu-id="92388-818">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-818">Setting</span></span> |
| ---
<span data-ttu-id="92388-819">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-819">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-820">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-820">'Blazor'</span></span>
- <span data-ttu-id="92388-821">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-821">'Identity'</span></span>
- <span data-ttu-id="92388-822">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-822">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-823">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-823">'Razor'</span></span>
- <span data-ttu-id="92388-824">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-824">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-825">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-825">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-826">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-826">'Blazor'</span></span>
- <span data-ttu-id="92388-827">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-827">'Identity'</span></span>
- <span data-ttu-id="92388-828">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-828">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-829">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-829">'Razor'</span></span>
- <span data-ttu-id="92388-830">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-830">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-831">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-831">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-832">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-832">'Blazor'</span></span>
- <span data-ttu-id="92388-833">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-833">'Identity'</span></span>
- <span data-ttu-id="92388-834">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-834">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-835">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-835">'Razor'</span></span>
- <span data-ttu-id="92388-836">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-836">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-837">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-837">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-838">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-838">'Blazor'</span></span>
- <span data-ttu-id="92388-839">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-839">'Identity'</span></span>
- <span data-ttu-id="92388-840">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-840">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-841">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-841">'Razor'</span></span>
- <span data-ttu-id="92388-842">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-842">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-843">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-844">'Blazor'</span></span>
- <span data-ttu-id="92388-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-845">'Identity'</span></span>
- <span data-ttu-id="92388-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-847">'Razor'</span></span>
- <span data-ttu-id="92388-848">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-849">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-850">'Blazor'</span></span>
- <span data-ttu-id="92388-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-851">'Identity'</span></span>
- <span data-ttu-id="92388-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-853">'Razor'</span></span>
- <span data-ttu-id="92388-854">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-855">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-856">'Blazor'</span></span>
- <span data-ttu-id="92388-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-857">'Identity'</span></span>
- <span data-ttu-id="92388-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-859">'Razor'</span></span>
- <span data-ttu-id="92388-860">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-861">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-862">'Blazor'</span></span>
- <span data-ttu-id="92388-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-863">'Identity'</span></span>
- <span data-ttu-id="92388-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-865">'Razor'</span></span>
- <span data-ttu-id="92388-866">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-867">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-868">'Blazor'</span></span>
- <span data-ttu-id="92388-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-869">'Identity'</span></span>
- <span data-ttu-id="92388-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-871">'Razor'</span></span>
- <span data-ttu-id="92388-872">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-873">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-874">'Blazor'</span></span>
- <span data-ttu-id="92388-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-875">'Identity'</span></span>
- <span data-ttu-id="92388-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-877">'Razor'</span></span>
- <span data-ttu-id="92388-878">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-879">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-880">'Blazor'</span></span>
- <span data-ttu-id="92388-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-881">'Identity'</span></span>
- <span data-ttu-id="92388-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-883">'Razor'</span></span>
- <span data-ttu-id="92388-884">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-885">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-886">'Blazor'</span></span>
- <span data-ttu-id="92388-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-887">'Identity'</span></span>
- <span data-ttu-id="92388-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-889">'Razor'</span></span>
- <span data-ttu-id="92388-890">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-891">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-892">'Blazor'</span></span>
- <span data-ttu-id="92388-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-893">'Identity'</span></span>
- <span data-ttu-id="92388-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-895">'Razor'</span></span>
- <span data-ttu-id="92388-896">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-896">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-897">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-897">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-898">'Blazor'</span></span>
- <span data-ttu-id="92388-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-899">'Identity'</span></span>
- <span data-ttu-id="92388-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-901">'Razor'</span></span>
- <span data-ttu-id="92388-902">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-902">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-903">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il server IIS imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-903">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-904">Se `false`, il server fornisce solo un'identità per `HttpContext.User` e risponde alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-904">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-905">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-905">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-906">Per altre informazioni, vedere [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-906">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-907">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-907">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="92388-908">**Modello di hosting out-of-process**</span><span class="sxs-lookup"><span data-stu-id="92388-908">**Out-of-process hosting model**</span></span>

<span data-ttu-id="92388-909">Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="92388-909">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="92388-910">Nell'esempio seguente si impedisce all'app di popolare `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="92388-910">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="92388-911">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-911">Option</span></span>                         | <span data-ttu-id="92388-912">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-912">Default</span></span> | <span data-ttu-id="92388-913">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-913">Setting</span></span> |
| ---
<span data-ttu-id="92388-914">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-914">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-915">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-915">'Blazor'</span></span>
- <span data-ttu-id="92388-916">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-916">'Identity'</span></span>
- <span data-ttu-id="92388-917">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-917">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-918">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-918">'Razor'</span></span>
- <span data-ttu-id="92388-919">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-919">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-920">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-920">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-921">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-921">'Blazor'</span></span>
- <span data-ttu-id="92388-922">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-922">'Identity'</span></span>
- <span data-ttu-id="92388-923">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-923">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-924">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-924">'Razor'</span></span>
- <span data-ttu-id="92388-925">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-925">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-926">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-926">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-927">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-927">'Blazor'</span></span>
- <span data-ttu-id="92388-928">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-928">'Identity'</span></span>
- <span data-ttu-id="92388-929">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-929">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-930">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-930">'Razor'</span></span>
- <span data-ttu-id="92388-931">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-931">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-932">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-932">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-933">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-933">'Blazor'</span></span>
- <span data-ttu-id="92388-934">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-934">'Identity'</span></span>
- <span data-ttu-id="92388-935">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-935">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-936">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-936">'Razor'</span></span>
- <span data-ttu-id="92388-937">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-937">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-938">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-938">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-939">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-939">'Blazor'</span></span>
- <span data-ttu-id="92388-940">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-940">'Identity'</span></span>
- <span data-ttu-id="92388-941">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-941">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-942">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-942">'Razor'</span></span>
- <span data-ttu-id="92388-943">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-943">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-944">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-944">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-945">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-945">'Blazor'</span></span>
- <span data-ttu-id="92388-946">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-946">'Identity'</span></span>
- <span data-ttu-id="92388-947">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-947">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-948">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-948">'Razor'</span></span>
- <span data-ttu-id="92388-949">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-949">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-950">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-950">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-951">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-951">'Blazor'</span></span>
- <span data-ttu-id="92388-952">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-952">'Identity'</span></span>
- <span data-ttu-id="92388-953">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-953">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-954">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-954">'Razor'</span></span>
- <span data-ttu-id="92388-955">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-955">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-956">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-956">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-957">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-957">'Blazor'</span></span>
- <span data-ttu-id="92388-958">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-958">'Identity'</span></span>
- <span data-ttu-id="92388-959">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-959">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-960">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-960">'Razor'</span></span>
- <span data-ttu-id="92388-961">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-961">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-962">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-962">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-963">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-963">'Blazor'</span></span>
- <span data-ttu-id="92388-964">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-964">'Identity'</span></span>
- <span data-ttu-id="92388-965">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-965">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-966">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-966">'Razor'</span></span>
- <span data-ttu-id="92388-967">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-967">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-968">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-968">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-969">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-969">'Blazor'</span></span>
- <span data-ttu-id="92388-970">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-970">'Identity'</span></span>
- <span data-ttu-id="92388-971">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-971">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-972">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-972">'Razor'</span></span>
- <span data-ttu-id="92388-973">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-973">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-974">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-974">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-975">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-975">'Blazor'</span></span>
- <span data-ttu-id="92388-976">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-976">'Identity'</span></span>
- <span data-ttu-id="92388-977">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-977">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-978">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-978">'Razor'</span></span>
- <span data-ttu-id="92388-979">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-979">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-980">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-980">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-981">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-981">'Blazor'</span></span>
- <span data-ttu-id="92388-982">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-982">'Identity'</span></span>
- <span data-ttu-id="92388-983">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-983">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-984">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-984">'Razor'</span></span>
- <span data-ttu-id="92388-985">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-985">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-986">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-986">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-987">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-987">'Blazor'</span></span>
- <span data-ttu-id="92388-988">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-988">'Identity'</span></span>
- <span data-ttu-id="92388-989">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-989">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-990">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-990">'Razor'</span></span>
- <span data-ttu-id="92388-991">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-991">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-992">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-992">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-993">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-993">'Blazor'</span></span>
- <span data-ttu-id="92388-994">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-994">'Identity'</span></span>
- <span data-ttu-id="92388-995">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-995">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-996">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-996">'Razor'</span></span>
- <span data-ttu-id="92388-997">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-997">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-998">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il [middleware di integrazione di IIS](#enable-the-iisintegration-components) imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-998">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-999">Se `false`, il middleware fornisce solo un'identità per `HttpContext.User` e risponde solo alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-999">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-1000">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1000">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-1001">Per altre informazioni, vedere l'argomento [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1001">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="92388-1002">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-1002">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="92388-1003">| | `ForwardClientCertificate`     | `true`  | Se `true` e l' `MS-ASPNETCORE-CLIENTCERT` intestazione della richiesta è presente, `HttpContext.Connection.ClientCertificate` viene popolato.</span><span class="sxs-lookup"><span data-stu-id="92388-1003">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="92388-1004">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="92388-1004">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="92388-1005">Il [middleware di integrazione IIS](#enable-the-iisintegration-components), che consente di configurare il middleware delle intestazioni inoltrate, e il modulo ASP.NET Core sono configurati per inoltrare lo schema (HTTP/HTTPS) e l'indirizzo IP remoto di origine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1005">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="92388-1006">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="92388-1006">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="92388-1007">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="92388-1007">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="92388-1008">File web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1008">web.config file</span></span>

<span data-ttu-id="92388-1009">Il file *web.config* configura il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-1009">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-1010">La creazione, la trasformazione e la pubblicazione del file *web.config* sono operazioni gestite da una destinazione MSBuild (`_TransformWebConfig`) quando viene pubblicato il progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-1010">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="92388-1011">Questa destinazione si trova tra le destinazioni Web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="92388-1011">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="92388-1012">L'SDK è impostato all'inizio del file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-1012">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="92388-1013">Se nel progetto non è presente un file *web.config*, il file viene creato con un valore *processPath* e *argomenti* corretti per la configurazione del modulo ASP.NET Core. Il file viene quindi spostato nell'[output pubblicato](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="92388-1013">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="92388-1014">Se nel progetto è presente un file *web.config*, il file viene trasformato con il valore di *processPath* e gli *argomenti* corretti per la configurazione del modulo ASP.NET Core, quindi viene spostato nell'output pubblicato.</span><span class="sxs-lookup"><span data-stu-id="92388-1014">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="92388-1015">La trasformazione non modifica le impostazioni di configurazione di IIS incluse nel file.</span><span class="sxs-lookup"><span data-stu-id="92388-1015">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="92388-1016">Il file *web.config* può fornire ulteriori impostazioni di configurazione di IIS che controllano i moduli IIS attivi.</span><span class="sxs-lookup"><span data-stu-id="92388-1016">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="92388-1017">Per informazioni sui moduli IIS in grado di elaborare le richieste con le app di ASP.NET Core, vedere l'argomento [Moduli IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="92388-1017">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="92388-1018">Per impedire a Web SDK di trasformare il file *Web. config* , utilizzare la **\<IsTransformWebConfigDisabled>** proprietà nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-1018">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="92388-1019">Quando si disabilita la trasformazione del file in Web SDK, il valore di *processPath* e gli *argomenti* devono essere impostati manualmente dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="92388-1019">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="92388-1020">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-1020">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="92388-1021">Posizione del file web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1021">web.config file location</span></span>

<span data-ttu-id="92388-1022">Per configurare correttamente il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , il file *Web. config* deve essere presente nel percorso [radice del contenuto](xref:fundamentals/index#content-root) (in genere il percorso di base dell'app) dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="92388-1022">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="92388-1023">Corrisponde al percorso fisico del sito Web fornito a IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1023">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="92388-1024">Il file *web.config* deve essere presente nella radice dell'app per abilitare la pubblicazione di più app mediante Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-1024">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="92388-1025">I file sensibili sono presenti nel percorso fisico dell'app, ad esempio \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (commenti in formato documentazione XML) e \* \<assembly> . Deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="92388-1025">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="92388-1026">Quando il file *web.config* è presente e il sito viene avviato normalmente, IIS non rende disponibili questi file riservati se vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="92388-1026">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="92388-1027">Se il file *web.config* non è presente, ha un nome non corretto o non è in grado di configurare il sito per l'avvio normale, IIS potrebbe rendere disponibili pubblicamente i file riservati.</span><span class="sxs-lookup"><span data-stu-id="92388-1027">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="92388-1028">**Il file *Web. config* deve essere sempre presente nella distribuzione, denominato correttamente e in grado di configurare il sito per l'avvio normale. Non rimuovere mai il file *Web. config* da una distribuzione di produzione.**</span><span class="sxs-lookup"><span data-stu-id="92388-1028">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="92388-1029">Trasformare web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1029">Transform web.config</span></span>

<span data-ttu-id="92388-1030">Se è necessario trasformare *web.config* in fase di pubblicazione (ad esempio, impostare variabili di ambiente in base a configurazione, profilo o ambiente), vedere <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="92388-1030">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="92388-1031">Configurazione di IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1031">IIS configuration</span></span>

<span data-ttu-id="92388-1032">**Sistemi operativi Windows Server**</span><span class="sxs-lookup"><span data-stu-id="92388-1032">**Windows Server operating systems**</span></span>

<span data-ttu-id="92388-1033">Abilitare il ruolo del server **Server Web (IIS)** e stabilire i servizi di ruolo.</span><span class="sxs-lookup"><span data-stu-id="92388-1033">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="92388-1034">Usare la procedura guidata **Aggiungi ruoli e funzionalità** accessibile tramite il menu **Gestisci** o il collegamento in **Server Manager**.</span><span class="sxs-lookup"><span data-stu-id="92388-1034">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="92388-1035">Nel passaggio **Ruoli del server** selezionare la casella per **Server Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="92388-1035">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Il ruolo Server Web IIS viene selezionato nel passaggio dei ruoli del server Selezionare.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="92388-1037">Dopo il passaggio **Funzionalità**, viene caricato il passaggio**Servizi ruolo** per Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-1037">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="92388-1038">Selezionare i servizi ruolo IIS desiderati o accettare i servizi ruolo predefiniti specificati.</span><span class="sxs-lookup"><span data-stu-id="92388-1038">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![I servizi ruolo predefiniti vengono selezionati nel passaggio Selezionare i servizi ruolo.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="92388-1040">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1040">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-1041">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: sicurezza del **server Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-1041">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="92388-1042">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-1042">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-1043">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1043">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-1044">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-1044">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-1045">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-1045">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-1046">Per abilitare i WebSocket, espandere i nodi seguenti: sviluppo di applicazioni **server Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="92388-1046">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="92388-1047">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-1047">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-1048">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-1048">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-1049">Procedere con il passaggio **Conferma** per installare il ruolo del server web e i servizi.</span><span class="sxs-lookup"><span data-stu-id="92388-1049">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="92388-1050">Dopo l'installazione del ruolo **server Web (IIS)** non è necessario riavviare il server o IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1050">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="92388-1051">**Sistemi operativi desktop di Windows**</span><span class="sxs-lookup"><span data-stu-id="92388-1051">**Windows desktop operating systems**</span></span>

<span data-ttu-id="92388-1052">Abilitare **Console di gestione IIS** e **Servizi Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1052">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-1053">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Disattivare o attivare le funzionalità di Windows** (a sinistra dello schermo).</span><span class="sxs-lookup"><span data-stu-id="92388-1053">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="92388-1054">Aprire il nodo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="92388-1054">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="92388-1055">Aprire il nodo **Strumenti di gestione Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1055">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="92388-1056">Selezionare la casella per **Console di gestione IIS**.</span><span class="sxs-lookup"><span data-stu-id="92388-1056">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="92388-1057">Selezionare la casella per **Servizi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1057">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-1058">Accettare le funzionalità predefinite per **Servizi World Wide Web** o personalizzare le funzionalità IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1058">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="92388-1059">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1059">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-1060">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: **World Wide Web sicurezza dei servizi**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-1060">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="92388-1061">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-1061">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-1062">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1062">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-1063">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-1063">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-1064">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-1064">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-1065">Per abilitare i WebSocket, espandere i nodi seguenti: funzionalità di sviluppo di applicazioni di **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="92388-1065">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="92388-1066">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-1066">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-1067">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-1067">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-1068">Se l'installazione di IIS richiede un riavvio, riavviare il sistema.</span><span class="sxs-lookup"><span data-stu-id="92388-1068">If the IIS installation requires a restart, restart the system.</span></span>

![La console di gestione IIS e servizi World Wide Web sono selezionati nelle funzionalità di Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="92388-1070">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-1070">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="92388-1071">Installare il *bundle di hosting .NET Core* nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1071">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="92388-1072">Il bundle installa il runtime di .NET Core, la libreria di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-1072">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-1073">Il modulo consente l'esecuzione delle app ASP.NET Core dietro IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1073">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92388-1074">Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="92388-1074">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="92388-1075">Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1075">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="92388-1076">Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="92388-1076">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="92388-1077">Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="92388-1077">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="92388-1078">Download</span><span class="sxs-lookup"><span data-stu-id="92388-1078">Download</span></span>

1. <span data-ttu-id="92388-1079">Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="92388-1079">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="92388-1080">Selezionare la versione desiderata di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1080">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="92388-1081">Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="92388-1081">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="92388-1082">Scaricare il programma di installazione usando il collegamento **bundle di hosting** .</span><span class="sxs-lookup"><span data-stu-id="92388-1082">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="92388-1083">Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="92388-1083">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="92388-1084">Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="92388-1084">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="92388-1085">Installare il bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="92388-1085">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="92388-1086">Eseguire il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-1086">Run the installer on the server.</span></span> <span data-ttu-id="92388-1087">Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1087">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="92388-1088">`OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1088">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="92388-1089">`OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1089">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="92388-1090">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-1090">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-1091">`OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="92388-1091">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="92388-1092">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-1092">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-1093">`OPT_NO_X86=1`: Ignora l'installazione di Runtime x86.</span><span class="sxs-lookup"><span data-stu-id="92388-1093">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="92388-1094">Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-1094">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="92388-1095">Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-1095">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="92388-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa (*ApplicationHost. config*) si trova nello stesso computer dell'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1096">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="92388-1097">*Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="92388-1097">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="92388-1098">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="92388-1098">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="92388-1099">Riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="92388-1099">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="92388-1100">Il riavvio di IIS rileva una modifica alla variabile di ambiente di sistema PATH apportata dal programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1100">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="92388-1101">Non è necessario arrestare manualmente singoli siti in IIS durante l'installazione del bundle di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1101">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="92388-1102">Le app ospitate (siti IIS) vengono riavviate quando IIS viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="92388-1102">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="92388-1103">Le app vengono riavviate quando ricevono la prima richiesta, incluso il [modulo di inizializzazione dell'applicazione](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="92388-1103">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="92388-1104">ASP.NET Core adotta il comportamento di rollforward per le versioni di patch dei pacchetti di Framework condivisi.</span><span class="sxs-lookup"><span data-stu-id="92388-1104">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="92388-1105">Quando le app ospitate da IIS vengono riavviate con IIS, le app vengono caricate con le ultime versioni di patch dei pacchetti a cui si fa riferimento quando ricevono la prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1105">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="92388-1106">Se IIS non viene riavviato, le app vengono riavviate e presentano un comportamento di rollforward quando i processi di lavoro vengono riciclati e ricevono la prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1106">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="92388-1107">Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="92388-1107">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="92388-1108">Installare la Distribuzione Web quando si pubblica con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1108">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="92388-1109">Quando si distribuiscono app ai server con [Distribuzione Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installare la versione più recente di Distribuzione Web nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-1109">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="92388-1110">Per installare Distribuzione Web usare l'[Installazione guidata piattaforma Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) o ottenere direttamente un programma di installazione in [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="92388-1110">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="92388-1111">Il metodo preferito consiste nell'usare WebPI.</span><span class="sxs-lookup"><span data-stu-id="92388-1111">The preferred method is to use WebPI.</span></span> <span data-ttu-id="92388-1112">WebPI offre un'installazione autonoma e una configurazione per i provider di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1112">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="92388-1113">Creare il sito IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1113">Create the IIS site</span></span>

1. <span data-ttu-id="92388-1114">Nel sistema host creare una cartella per contenere le cartelle e i file pubblicati dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1114">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="92388-1115">In un passaggio successivo, il percorso della cartella viene fornito a IIS come percorso fisico dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1115">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="92388-1116">Per altre informazioni sulla cartella di distribuzione e il layout dei file di un'app, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="92388-1116">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="92388-1117">In Gestione IIS aprire il nodo del server nel pannello **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="92388-1117">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="92388-1118">Fare clic con il pulsante destro del mouse sulla cartella **Siti**.</span><span class="sxs-lookup"><span data-stu-id="92388-1118">Right-click the **Sites** folder.</span></span> <span data-ttu-id="92388-1119">Scegliere **Aggiungi sito Web** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-1119">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-1120">Specificare un **Nome del sito** e impostare il **Percorso fisico** per la cartella di distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1120">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="92388-1121">Specificare la configurazione dell' **associazione** e creare il sito Web selezionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="92388-1121">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Specificare il nome del sito, il percorso fisico e il nome Host nel passaggio Aggiungi sito Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="92388-1123">Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate,</span><span class="sxs-lookup"><span data-stu-id="92388-1123">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="92388-1124">poiché possono introdurre vulnerabilità a livello di sicurezza nell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1124">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="92388-1125">Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili.</span><span class="sxs-lookup"><span data-stu-id="92388-1125">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="92388-1126">Usare nomi host espliciti al posto di caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="92388-1126">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="92388-1127">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="92388-1127">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="92388-1128">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1128">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="92388-1129">Nel nodo del server selezionare **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1129">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="92388-1130">Fare clic con il pulsante destro del mouse sul pool di applicazioni del sito e scegliere **Impostazioni di base** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-1130">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-1131">Nella finestra **Modifica pool di applicazioni** impostare **Versione .NET CLR** su **Nessun codice gestito**.</span><span class="sxs-lookup"><span data-stu-id="92388-1131">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Impostare Nessun codice gestito per la versione .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="92388-1133">ASP.NET Core viene eseguito in un processo separato e gestisce il runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-1133">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="92388-1134">ASP.NET Core non si basa sul caricamento di Common Language Runtime (CLR di .NET) desktop. Core Common Language Runtime (CoreCLR) for .NET Core viene avviato per ospitare l'app nel processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92388-1134">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="92388-1135">L'impostazione di **Versione .NET CLR** su **Nessun codice gestito** è facoltativa ma consigliata.</span><span class="sxs-lookup"><span data-stu-id="92388-1135">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="92388-1136">*ASP.NET Core 2.2 o versioni successive*: per una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) a 64 bit (x64) che usa il [modello di hosting In-Process](#in-process-hosting-model), disabilitare il pool di app per i processi a 32 bit (x86).</span><span class="sxs-lookup"><span data-stu-id="92388-1136">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="92388-1137">Nella barra laterale **Azioni** in Gestione IIS > **Pool di applicazioni** selezionare **Impostazioni predefinite pool di applicazioni** o **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-1137">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="92388-1138">Individuare **Attiva applicazioni a 32 bit** e impostare il valore su `False`.</span><span class="sxs-lookup"><span data-stu-id="92388-1138">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="92388-1139">Questa impostazione non viene applicata alle app distribuite per l'[hosting out-of-process](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="92388-1139">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="92388-1140">Confermare che l'identità del modello del processo disponga delle autorizzazioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="92388-1140">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="92388-1141">Se l'identità predefinita del pool di app (**modello**  >  **Identity** di processo) viene modificata da **ApplicationPoolIdentity** a un'altra identità, verificare che la nuova identità disponga delle autorizzazioni necessarie per accedere alla cartella dell'app, al database e ad altre risorse richieste.</span><span class="sxs-lookup"><span data-stu-id="92388-1141">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="92388-1142">Ad esempio, il pool di applicazioni richiede l'accesso in lettura e scrittura alle cartelle in cui l'app legge e scrive i file.</span><span class="sxs-lookup"><span data-stu-id="92388-1142">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="92388-1143">**Configurazione dell'autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1143">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="92388-1144">Per altre informazioni, vedere [Configurare l'autenticazione Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1144">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="92388-1145">Distribuire l'app</span><span class="sxs-lookup"><span data-stu-id="92388-1145">Deploy the app</span></span>

<span data-ttu-id="92388-1146">Distribuire l'app nella cartella **Percorso fisico** di IIS specificata nella sezione [Creare il sito IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="92388-1146">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="92388-1147">[Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) è il meccanismo consigliato per la distribuzione, ma esistono diverse opzioni per spostare l'app dalla cartella di *pubblicazione* del progetto alla cartella di distribuzione del sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1147">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="92388-1148">Distribuzione web con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1148">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="92388-1149">Vedere l'argomento [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Profili di pubblicazione Visual Studio per la distribuzione di app ASP.NET Core) per informazioni su come creare un profilo di pubblicazione da usare con Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-1149">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="92388-1150">Se il provider di hosting fornisce un profilo di pubblicazione o il supporto per crearne uno, scaricare il profilo e importarlo usando la finestra di dialogo **Pubblica** di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="92388-1150">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Pagina della finestra di dialogo Pubblica](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="92388-1152">Distribuzione Web all'esterno di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1152">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="92388-1153">È anche possibile usare [Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) all'esterno di Visual Studio dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="92388-1153">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="92388-1154">Per altre informazioni sulla distribuzione, vedere [Strumento Distribuzione Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="92388-1154">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="92388-1155">Alternative a Distribuzione web</span><span class="sxs-lookup"><span data-stu-id="92388-1155">Alternatives to Web Deploy</span></span>

<span data-ttu-id="92388-1156">Usare uno dei metodi disponibili, ad esempio la copia manuale, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/), per spostare l'app nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1156">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="92388-1157">Per altre informazioni sulla distribuzione di ASP.NET Core in IIS, vedere la sezione [Risorse di distribuzione per amministratori IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="92388-1157">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="92388-1158">Esplorare il sito Web</span><span class="sxs-lookup"><span data-stu-id="92388-1158">Browse the website</span></span>

<span data-ttu-id="92388-1159">Dopo aver distribuito l'app nel sistema di hosting, effettuare una richiesta a uno degli endpoint pubblici dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1159">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="92388-1160">Nell'esempio seguente il sito è associato al **nome host** di IIS `www.mysite.com` sulla **porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="92388-1160">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="92388-1161">Viene effettuata una richiesta a `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="92388-1161">A request is made to `http://www.mysite.com`:</span></span>

![Il browser Microsoft Edge ha caricato la pagina di avvio IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="92388-1163">File di distribuzione bloccati</span><span class="sxs-lookup"><span data-stu-id="92388-1163">Locked deployment files</span></span>

<span data-ttu-id="92388-1164">I file nella cartella di distribuzione sono bloccati durante l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1164">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="92388-1165">I file bloccati non possono essere sovrascritti durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-1165">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="92388-1166">Per rilasciare i file bloccati in una distribuzione, arrestare il pool di applicazioni usando **uno** dei metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1166">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-1167">Usare Distribuzione Web e fare riferimento a `Microsoft.NET.Sdk.Web` nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-1167">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="92388-1168">Nella radice della directory dell'app web viene posizionato un file *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="92388-1168">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="92388-1169">Quando il file è presente, il modulo ASP.NET Core arresta normalmente l'app e rende disponibile il file *app_offline.htm* durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-1169">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="92388-1170">Per altre informazioni, vedere la [Guida di riferimento per la configurazione del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="92388-1170">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="92388-1171">Arrestare manualmente il pool di applicazioni in Gestione IIS sul server.</span><span class="sxs-lookup"><span data-stu-id="92388-1171">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="92388-1172">Usare PowerShell per eliminare *App_offline. htm* (richiede PowerShell 5 o versione successiva):</span><span class="sxs-lookup"><span data-stu-id="92388-1172">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="92388-1173">Protezione dei dati</span><span class="sxs-lookup"><span data-stu-id="92388-1173">Data protection</span></span>

<span data-ttu-id="92388-1174">Lo [stack di protezione dei dati di ASP.NET Core](xref:security/data-protection/introduction) viene usato da diversi [middleware](xref:fundamentals/middleware/index) di ASP.NET Core, inclusi quelli usati nell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1174">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="92388-1175">Anche se le DPAPI (Data Protection API) non vengono chiamate dal codice dell'utente, è consigliabile configurare la protezione dati per la creazione di un [archivio di chiavi](xref:security/data-protection/implementation/key-management) crittografiche permanente con uno script di distribuzione o nel codice dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1175">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="92388-1176">Se non si configura la protezione dei dati, le chiavi vengono mantenute in memoria ed eliminate al riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1176">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="92388-1177">Se il gruppo di chiavi viene archiviato in memoria quando l'app viene riavviata:</span><span class="sxs-lookup"><span data-stu-id="92388-1177">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="92388-1178">Tutti i token di autenticazione basati su cookie vengono invalidati.</span><span class="sxs-lookup"><span data-stu-id="92388-1178">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="92388-1179">Gli utenti devono ripetere l'accesso alla richiesta successiva.</span><span class="sxs-lookup"><span data-stu-id="92388-1179">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="92388-1180">Tutti i dati protetti con il gruppo di chiavi non possono più essere decrittografati.</span><span class="sxs-lookup"><span data-stu-id="92388-1180">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="92388-1181">Questo può includere i [token CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e i [cookie TEMPDATA di ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="92388-1181">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="92388-1182">Per configurare la protezione dati in IIS in modo da rendere permanente il gruppo di chiavi, usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1182">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-1183">**Creare chiavi del Registro di sistema di protezione dati**</span><span class="sxs-lookup"><span data-stu-id="92388-1183">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="92388-1184">Le chiavi di protezione dati usate dalle app ASP.NET Core vengono archiviate nel Registro di sistema esterno alle app.</span><span class="sxs-lookup"><span data-stu-id="92388-1184">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="92388-1185">Per mantenere le chiavi per una determinata app, creare chiavi del Registro di sistema per il pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1185">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="92388-1186">Per le installazioni IIS autonome non web farm è possibile usare lo [script PowerShell Data Protection Provision-AutoGenKeys.ps1 (Provisioning di protezione dati-AutoGenKeys.ps1)](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) per ogni pool di app usato con un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1186">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="92388-1187">Questo script crea una chiave del Registro di sistema nel registro HKLM accessibile solo all'account del processo di lavoro del pool di applicazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1187">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="92388-1188">Le chiavi vengono crittografate a riposo tramite DPAPI con una chiave a livello del computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1188">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="92388-1189">In scenari di web farm un'app può essere configurata in modo da usare un percorso UNC in cui archiviare il proprio gruppo di chiavi di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="92388-1189">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="92388-1190">Per impostazione predefinita, le chiavi di protezione dati non vengono crittografate.</span><span class="sxs-lookup"><span data-stu-id="92388-1190">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="92388-1191">Assicurarsi che le autorizzazioni file per la condivisione di rete siano limitate all'account di Windows in cui viene eseguita l'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1191">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="92388-1192">È possibile usare un certificato X509 per la protezione delle chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="92388-1192">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="92388-1193">Considerare l'implementazione di un meccanismo per consentire agli utenti di caricare i certificati: inserire i certificati nell'archivio certificati attendibili dell'utente e assicurarsi che siano disponibili in tutti i computer in cui viene eseguita l'app dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1193">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="92388-1194">Vedere [Configurare la protezione dati di ASP.NET Core](xref:security/data-protection/configuration/overview) per altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="92388-1194">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="92388-1195">**Configurare il pool di applicazioni IIS per caricare il profilo utente**</span><span class="sxs-lookup"><span data-stu-id="92388-1195">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="92388-1196">Questa impostazione si trova nella sezione **Modello del processo** in **Impostazioni avanzate** per il pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-1196">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="92388-1197">Impostare **Caricamento del profilo utente** su `True`.</span><span class="sxs-lookup"><span data-stu-id="92388-1197">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="92388-1198">Con l'impostazione `True` le chiavi vengono archiviate nella directory del profilo utente e protette tramite DPAPI con una chiave specifica per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1198">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="92388-1199">Le chiavi vengono salvate in modo permanente nella cartella *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="92388-1199">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="92388-1200">Deve essere abilitato anche l'[attributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-1200">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="92388-1201">Il valore predefinito di `setProfileEnvironment` è `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-1201">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="92388-1202">In alcuni scenari (ad esempio, per il sistema operativo Windows), `setProfileEnvironment` è impostato su `false`.</span><span class="sxs-lookup"><span data-stu-id="92388-1202">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="92388-1203">Se le chiavi non vengono archiviate nella directory del profilo utente come previsto:</span><span class="sxs-lookup"><span data-stu-id="92388-1203">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="92388-1204">Passare alla cartella *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="92388-1204">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="92388-1205">Aprire il file *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="92388-1205">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="92388-1206">Individuare l'elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="92388-1206">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="92388-1207">Verificare che l'attributo `setProfileEnvironment` non sia presente, condizione che corrisponde all'impostazione predefinita `true`, o impostare in modo esplicito il valore dell'attributo su `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-1207">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="92388-1208">**Usare il file system come archivio del gruppo di chiavi**</span><span class="sxs-lookup"><span data-stu-id="92388-1208">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="92388-1209">Modificare il codice dell'app per [usare il file system come archivio del gruppo di chiavi](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-1209">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="92388-1210">Usare un certificato X509 per proteggere il gruppo di chiavi e verificare che sia un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-1210">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="92388-1211">Se il certificato è autofirmato, inserirlo nell'archivio radice attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-1211">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="92388-1212">Quando si usa IIS in una web farm:</span><span class="sxs-lookup"><span data-stu-id="92388-1212">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="92388-1213">Usare una condivisione di file a cui possono accedere tutti i computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1213">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="92388-1214">Distribuire un certificato X509 in ogni computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1214">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="92388-1215">Configurare [protezione dei dati nel codice](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-1215">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="92388-1216">**Impostare criteri a livello di computer per la protezione dei dati**</span><span class="sxs-lookup"><span data-stu-id="92388-1216">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="92388-1217">Il sistema di protezione dei dati ha un supporto limitato per l'impostazione di [criteri a livello di computer](xref:security/data-protection/configuration/machine-wide-policy) predefiniti per tutte le app che usano le API di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="92388-1217">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="92388-1218">Per altre informazioni, vedere <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92388-1218">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="92388-1219">Directory virtuali</span><span class="sxs-lookup"><span data-stu-id="92388-1219">Virtual Directories</span></span>

<span data-ttu-id="92388-1220">Le [directory virtuali IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) non sono supportate con le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1220">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="92388-1221">Un'app può essere ospitata come [applicazione secondaria](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="92388-1221">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="92388-1222">Applicazioni secondarie</span><span class="sxs-lookup"><span data-stu-id="92388-1222">Sub-applications</span></span>

<span data-ttu-id="92388-1223">Un'app ASP.NET Core può essere ospitata come [applicazione secondaria di IIS (app secondaria)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="92388-1223">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="92388-1224">Il percorso dell'app secondaria diventa parte dell'URL dell'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-1224">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="92388-1225">I collegamenti ad asset statici all'interno dell'app secondaria devono usare la notazione con tilde-barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="92388-1225">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="92388-1226">La notazione con tilde-barra attiva un [helper tag](xref:mvc/views/tag-helpers/intro) per anteporre la base del percorso dell'app secondaria al collegamento relativo sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="92388-1226">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="92388-1227">Per un'app secondaria in `/subapp_path`, il rendering di un'immagine collegata con `src="~/image.png"` viene eseguito come `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="92388-1227">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="92388-1228">Il middleware dei file statici dell'app radice non elabora la richiesta di file statici.</span><span class="sxs-lookup"><span data-stu-id="92388-1228">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="92388-1229">La richiesta viene elaborata dal middleware dei file statici dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1229">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="92388-1230">Se l'attributo `src` di un asset statico viene impostato su un percorso assoluto (ad esempio, `src="/image.png"`), il rendering del collegamento viene eseguito senza la base del percorso dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1230">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="92388-1231">Il middleware dei file statici dell'app radice prova a servire l'asset dalla [radice Web](xref:fundamentals/index#web-root) dell'app radice con conseguente risposta *404 - Non trovato*, a meno che l'asset statico non sia disponibile dal'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-1231">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="92388-1232">Per ospitare un'app ASP.NET Core come app secondaria in un'altra app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="92388-1232">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="92388-1233">Definire un pool di app per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1233">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="92388-1234">Impostare **Versione .NET CLR** su **Nessun codice gestito** perché Core Common Language Runtime (CoreCLR) for .NET Core viene avviato per ospitare l'app nel processo di lavoro e non CLR desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="92388-1234">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="92388-1235">Aggiungere il sito radice in Gestione IIS con l'applicazione secondaria in una cartella all'interno del sito principale.</span><span class="sxs-lookup"><span data-stu-id="92388-1235">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="92388-1236">Fare clic con il pulsante destro del mouse sulla cartella dell'applicazione secondaria in Gestione IIS e scegliere **Converti in applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-1236">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="92388-1237">Nella finestra di dialogo **Aggiungi applicazione** usare il pulsante **Seleziona** per **Pool di applicazioni** per assegnare il pool di app creato per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1237">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="92388-1238">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1238">Select **OK**.</span></span>

<span data-ttu-id="92388-1239">L'assegnazione di un pool di app separato all'app secondaria è un requisito quando si usa il modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="92388-1239">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="92388-1240">Per ulteriori informazioni sul modello di hosting in-process e sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="92388-1240">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="92388-1241">Configurazione di IIS con web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1241">Configuration of IIS with web.config</span></span>

<span data-ttu-id="92388-1242">La configurazione di IIS è influenzata dalla sezione `<system.webServer>` di *web.config* per gli scenari IIS funzionali per le app ASP.NET Core con il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1242">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="92388-1243">Ad esempio, la configurazione di IIS è funzionale per la compressione dinamica.</span><span class="sxs-lookup"><span data-stu-id="92388-1243">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="92388-1244">Se IIS è configurato a livello di server per l'uso della compressione dinamica, l'elemento `<urlCompression>` nel file *web.config* dell'app può disabilitare questa impostazione per un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1244">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="92388-1245">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1245">For more information, see the following topics:</span></span>

* [<span data-ttu-id="92388-1246">Riferimento alla configurazione per\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="92388-1246">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="92388-1247">Per impostare le variabili di ambiente per singole app in esecuzione in pool di applicazioni isolate (supportate per IIS 10,0 o versioni successive), vedere la sezione *comando appcmd. exe* dell'argomento [ \<environmentVariables> variabili di ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) nella documentazione di riferimento di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1247">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="92388-1248">Sezioni di configurazione di web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1248">Configuration sections of web.config</span></span>

<span data-ttu-id="92388-1249">Le sezioni di configurazione di app ASP.NET 4.x in *web.config* non vengono usate dalle app ASP.NET Core per la configurazione:</span><span class="sxs-lookup"><span data-stu-id="92388-1249">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="92388-1250">Le applicazioni ASP.NET Core vengono configurate tramite altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1250">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="92388-1251">Per ulteriori informazioni, vedere [configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="92388-1251">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="92388-1252">Pool di applicazioni</span><span class="sxs-lookup"><span data-stu-id="92388-1252">Application Pools</span></span>

<span data-ttu-id="92388-1253">L'isolamento dei pool di app è determinato dal modello di hosting:</span><span class="sxs-lookup"><span data-stu-id="92388-1253">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="92388-1254">Hosting in-process: le app sono necessarie per l'esecuzione in pool di applicazioni separati.</span><span class="sxs-lookup"><span data-stu-id="92388-1254">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="92388-1255">Hosting out-of-process: è consigliabile isolare le app le une dalle altre eseguendo ogni app nel proprio pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1255">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="92388-1256">Nella finestra di dialogo **Aggiungi sito Web** è selezionato per impostazione predefinita un singolo pool di app per ogni app.</span><span class="sxs-lookup"><span data-stu-id="92388-1256">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="92388-1257">Quando si specifica un valore in **Nome del sito**, il testo viene automaticamente trasferito alla casella di testo **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1257">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="92388-1258">Quando si aggiunge il sito viene creato un nuovo pool di applicazioni con il nome del sito.</span><span class="sxs-lookup"><span data-stu-id="92388-1258">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="92388-1259">Pool di applicazioniIdentity</span><span class="sxs-lookup"><span data-stu-id="92388-1259">Application Pool Identity</span></span>

<span data-ttu-id="92388-1260">Un account di identità del pool di app consente di eseguire un'app in un account univoco, senza dover creare e gestire domini o account locali.</span><span class="sxs-lookup"><span data-stu-id="92388-1260">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="92388-1261">In IIS 8.0 o versioni successive il processo di lavoro amministrazione IIS (WAS) crea un account virtuale con il nome del nuovo pool di applicazioni ed esegue i processi di lavoro del pool di applicazioni inclusi nell'account per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1261">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="92388-1262">Nella console di gestione IIS in **Impostazioni avanzate** per il pool di applicazioni, verificare che **Identity** sia impostato per utilizzare **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="92388-1262">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Finestra di dialogo Impostazioni avanzate del pool di applicazione](index/_static/apppool-identity.png)

<span data-ttu-id="92388-1264">Il processo di gestione IIS crea un identificatore sicuro con il nome del pool di app nel sistema di sicurezza di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-1264">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="92388-1265">Le risorse possono essere protette mediante questa identità,</span><span class="sxs-lookup"><span data-stu-id="92388-1265">Resources can be secured using this identity.</span></span> <span data-ttu-id="92388-1266">che tuttavia non è un account utente reale e non viene visualizzata nella console di gestione utenti di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-1266">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="92388-1267">Se il processo di lavoro IIS richiede l'accesso con privilegi elevati all'app, modificare l'elenco di controllo di accesso (ACL) della directory contenente l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-1267">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="92388-1268">Aprire Esplora risorse, quindi spostarsi nella directory.</span><span class="sxs-lookup"><span data-stu-id="92388-1268">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="92388-1269">Fare clic con il pulsante destro del mouse sulla directory e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="92388-1269">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="92388-1270">Nella scheda **Sicurezza** selezionare il pulsante **Modifica** e quindi il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="92388-1270">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="92388-1271">Fare clic sul pulsante **Percorsi** e verificare che il sistema sia selezionato.</span><span class="sxs-lookup"><span data-stu-id="92388-1271">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="92388-1272">Immettere **IIS AppPool\\<nome_pool_applicazioni>** nell'area **Immettere i nomi degli oggetti da selezionare**.</span><span class="sxs-lookup"><span data-stu-id="92388-1272">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="92388-1273">Fare clic sul pulsante **Controlla nomi**.</span><span class="sxs-lookup"><span data-stu-id="92388-1273">Select the **Check Names** button.</span></span> <span data-ttu-id="92388-1274">Per *DefaultAppPool* controllare i nomi usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-1274">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="92388-1275">Quando il pulsante **Controlla nomi** è selezionato, nell'area dei nomi degli oggetti viene indicato un valore di **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-1275">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="92388-1276">Non è possibile immettere il nome del pool di applicazioni direttamente nell'area dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-1276">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="92388-1277">Usare il formato **IIS AppPool\\<nome_pool_applicazioni>** durante il controllo dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-1277">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: il nome del pool di applicazioni "DefaultAppPool" viene aggiunto in coda a "IIS AppPool\" nell'area dei nomi degli oggetti prima di selezionare "Controlla nomi".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="92388-1279">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1279">Select **OK**.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: dopo aver selezionato "Controlla nomi", il nome dell'oggetto "DefaultAppPool" viene visualizzato nell'area dei nomi degli oggetti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="92388-1281">Le autorizzazioni di lettura ed esecuzione devono essere concesse per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1281">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="92388-1282">Fornire autorizzazioni aggiuntive in base alle necessità.</span><span class="sxs-lookup"><span data-stu-id="92388-1282">Provide additional permissions as needed.</span></span>

<span data-ttu-id="92388-1283">L'accesso può essere concesso anche dal prompt dei comandi usando lo strumento **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="92388-1283">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="92388-1284">Usando *DefaultAppPool* come esempio, viene eseguito il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="92388-1284">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="92388-1285">Per altre informazioni, vedere l'argomento [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="92388-1285">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="92388-1286">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="92388-1286">HTTP/2 support</span></span>

<span data-ttu-id="92388-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato con ASP.NET Core negli scenari di distribuzione IIS seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1287">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="92388-1288">In-Process</span><span class="sxs-lookup"><span data-stu-id="92388-1288">In-process</span></span>
  * <span data-ttu-id="92388-1289">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="92388-1289">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92388-1290">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="92388-1290">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="92388-1291">Out-of-process</span><span class="sxs-lookup"><span data-stu-id="92388-1291">Out-of-process</span></span>
  * <span data-ttu-id="92388-1292">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="92388-1292">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="92388-1293">Le connessioni server perimetrali pubbliche usano HTTP/2, mentre la connessione con proxy inverso al [server Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-1293">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="92388-1294">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="92388-1294">TLS 1.2 or later connection</span></span>

<span data-ttu-id="92388-1295">Per una distribuzione In-Process, se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="92388-1295">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="92388-1296">Per una distribuzione out-of-process, se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="92388-1296">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="92388-1297">Per altre informazioni sui modelli di hosting in-process e out-of-process, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-1297">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="92388-1298">HTTP/2 è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1298">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="92388-1299">Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-1299">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="92388-1300">Per altre informazioni sulla configurazione di HTTP/2 con le distribuzioni IIS, vedere [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-1300">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="92388-1301">Richieste preliminari CORS</span><span class="sxs-lookup"><span data-stu-id="92388-1301">CORS preflight requests</span></span>

<span data-ttu-id="92388-1302">*Questa sezione si applica solo alle app ASP.NET Core destinate a .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="92388-1302">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="92388-1303">Per un'app ASP.NET Core destinata a .NET Framework, le richieste OPTIONS non vengono passate all'app per impostazione predefinita in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1303">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="92388-1304">Per informazioni su come configurare i gestori IIS dell'app in *Web. config* per passare le richieste di opzioni, vedere [abilitare le richieste tra le origini in API Web ASP.NET 2:](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)funzionamento di CORS.</span><span class="sxs-lookup"><span data-stu-id="92388-1304">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="92388-1305">Modulo di inizializzazione dell'applicazione e timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-1305">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="92388-1306">Per l'hosting in IIS dal modulo ASP.NET Core versione 2:</span><span class="sxs-lookup"><span data-stu-id="92388-1306">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="92388-1307">[Modulo di inizializzazione dell'applicazione](#application-initialization-module): l'app è ospitata [in-process](#in-process-hosting-model) o [out-of-process](#out-of-process-hosting-model) può essere configurata per l'avvio automatico in un processo di lavoro o il riavvio del server.</span><span class="sxs-lookup"><span data-stu-id="92388-1307">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="92388-1308">[Timeout di inattività](#idle-timeout): le app ospitate [in-process](#in-process-hosting-model) possono essere configurate in modo da non eseguire il timeout durante i periodi di inattività.</span><span class="sxs-lookup"><span data-stu-id="92388-1308">[Idle Timeout](#idle-timeout): App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="92388-1309">Modulo Inizializzazione applicazione</span><span class="sxs-lookup"><span data-stu-id="92388-1309">Application Initialization Module</span></span>

<span data-ttu-id="92388-1310">*Si applica alle app ospitate in-process e out-of-process.*</span><span class="sxs-lookup"><span data-stu-id="92388-1310">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="92388-1311">[Inizializzazione applicazione di IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) è una funzionalità di IIS che invia una richiesta HTTP all'app quando il pool di app viene avviato o riciclato.</span><span class="sxs-lookup"><span data-stu-id="92388-1311">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="92388-1312">La richiesta attiva l'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1312">The request triggers the app to start.</span></span> <span data-ttu-id="92388-1313">Per impostazione predefinita, IIS invia una richiesta all'URL radice dell'app (`/`) per inizializzare l'app (vedere le [risorse aggiuntive](#application-initialization-module-and-idle-timeout-additional-resources) per altri dettagli sulla configurazione).</span><span class="sxs-lookup"><span data-stu-id="92388-1313">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="92388-1314">Verificare che la funzionalità del ruolo Inizializzazione applicazione di IIS sia abilitata:</span><span class="sxs-lookup"><span data-stu-id="92388-1314">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="92388-1315">Nei sistemi desktop Windows 7 o versioni successive quando si usa IIS in locale:</span><span class="sxs-lookup"><span data-stu-id="92388-1315">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="92388-1316">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Disattivare o attivare le funzionalità di Windows** (a sinistra dello schermo).</span><span class="sxs-lookup"><span data-stu-id="92388-1316">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="92388-1317">Aprire **Internet Information Services** > **Servizi Web** > **Funzionalità per lo sviluppo di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1317">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="92388-1318">Selezionare la casella di controllo **Inizializzazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-1318">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="92388-1319">In Windows Server 2008 R2 o versioni successive:</span><span class="sxs-lookup"><span data-stu-id="92388-1319">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="92388-1320">Aprire l'**Aggiunta guidata ruoli e funzionalità**.</span><span class="sxs-lookup"><span data-stu-id="92388-1320">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="92388-1321">Nel pannello **Selezione servizi ruolo** aprire il nodo **Sviluppo applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1321">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="92388-1322">Selezionare la casella di controllo **Inizializzazione applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-1322">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="92388-1323">Per abilitare il modulo Inizializzazione applicazione per il sito, usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1323">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="92388-1324">In Gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-1324">Using IIS Manager:</span></span>

  1. <span data-ttu-id="92388-1325">Selezionare **Pool di applicazioni** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1325">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="92388-1326">Fare clic con il pulsante destro del mouse sul pool di app dell'app nell'elenco e scegliere **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-1326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="92388-1327">L'impostazione predefinita per **Modalità avvio** è **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="92388-1327">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="92388-1328">Impostare **Modalità avvio** su **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="92388-1328">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="92388-1329">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1329">Select **OK**.</span></span>
  1. <span data-ttu-id="92388-1330">Aprire il nodo **Siti** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1330">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="92388-1331">Fare clic con il pulsante destro del mouse sull'app e scegliere **Gestisci sito Web** > **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-1331">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="92388-1332">L'impostazione predefinita di **Precaricamento abilitato** è **False**.</span><span class="sxs-lookup"><span data-stu-id="92388-1332">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="92388-1333">Impostare **Precaricamento abilitato** su **True**.</span><span class="sxs-lookup"><span data-stu-id="92388-1333">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="92388-1334">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1334">Select **OK**.</span></span>

* <span data-ttu-id="92388-1335">Usando *web.config* aggiungere l'elemento `<applicationInitialization>` con `doAppInitAfterRestart` impostato su `true` per gli elementi `<system.webServer>` nel file *web.config* dell'app:</span><span class="sxs-lookup"><span data-stu-id="92388-1335">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="92388-1336">Timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-1336">Idle Timeout</span></span>

<span data-ttu-id="92388-1337">*Si applica solo alle app ospitate in-process.*</span><span class="sxs-lookup"><span data-stu-id="92388-1337">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="92388-1338">Per evitare l'inattività dell'app, impostare il timeout di inattività del pool di app tramite Gestione IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-1338">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="92388-1339">Selezionare **Pool di applicazioni** nel pannello **Connessioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1339">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="92388-1340">Fare clic con il pulsante destro del mouse sul pool di app dell'app nell'elenco e scegliere **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-1340">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="92388-1341">L'impostazione predefinita di **Timeout di inattività (minuti)** è **20** minuti.</span><span class="sxs-lookup"><span data-stu-id="92388-1341">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="92388-1342">Impostare **Timeout di inattività (minuti)** su **0** (zero).</span><span class="sxs-lookup"><span data-stu-id="92388-1342">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="92388-1343">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1343">Select **OK**.</span></span>
1. <span data-ttu-id="92388-1344">Riciclare il processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92388-1344">Recycle the worker process.</span></span>

<span data-ttu-id="92388-1345">Per evitare il timeout delle app ospitate [out-of-process](#out-of-process-hosting-model), usare uno degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1345">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="92388-1346">Effettuare il ping dell'app da un servizio esterno per mantenerla in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-1346">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="92388-1347">Se l'app ospita solo servizi in background, evitare l'hosting IIS e usare un [servizio Windows per ospitare l'app ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="92388-1347">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="92388-1348">Risorse aggiuntive per il modulo Inizializzazione applicazione e il timeout di inattività</span><span class="sxs-lookup"><span data-stu-id="92388-1348">Application Initialization Module and Idle Timeout additional resources</span></span>

* <span data-ttu-id="92388-1349">[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) (Inizializzazione delle applicazioni in IIS 8.0)</span><span class="sxs-lookup"><span data-stu-id="92388-1349">[IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)</span></span>
* <span data-ttu-id="92388-1350">[Inizializzazione \<applicationInitialization> dell'applicazione ](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="92388-1350">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="92388-1351">[Impostazioni del modello di processo per un \<processModel> pool di applicazioni ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="92388-1351">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="92388-1352">Risorse di distribuzione per amministratori IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1352">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="92388-1353">Documentazione di ISS</span><span class="sxs-lookup"><span data-stu-id="92388-1353">IIS documentation</span></span>](/iis)
* <span data-ttu-id="92388-1354">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-1354">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* [<span data-ttu-id="92388-1355">Distribuzione di applicazioni .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-1355">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="92388-1356">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="92388-1356">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="92388-1357">Il sito IIS ufficiale di Microsoft</span><span class="sxs-lookup"><span data-stu-id="92388-1357">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="92388-1358">Libreria di contenuti tecnici di Windows Server</span><span class="sxs-lookup"><span data-stu-id="92388-1358">Windows Server technical content library</span></span>](/windows-server/windows-server)
* <span data-ttu-id="92388-1359">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-1359">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)</span></span>
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="92388-1360">Per un'esercitazione sulla pubblicazione di un'app ASP.NET Core in un server IIS, vedere <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="92388-1360">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="92388-1361">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-1361">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="92388-1362">Sistemi operativi supportati</span><span class="sxs-lookup"><span data-stu-id="92388-1362">Supported operating systems</span></span>

<span data-ttu-id="92388-1363">Sono supportati i sistemi operativi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1363">The following operating systems are supported:</span></span>

* <span data-ttu-id="92388-1364">Windows 7 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-1364">Windows 7 or later</span></span>
* <span data-ttu-id="92388-1365">Windows Server 2008 R2 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="92388-1365">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="92388-1366">Il [server HTTP.sys](xref:fundamentals/servers/httpsys) (chiamato in precedenza WebListener) non funziona in una configurazione proxy inverso con IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1366">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="92388-1367">È necessario usare il [server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-1367">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="92388-1368">Per informazioni sull'hosting in Azure, vedere <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="92388-1368">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="92388-1369">Per linee guida per la risoluzione dei problemi, vedere <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="92388-1369">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="92388-1370">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="92388-1370">Supported platforms</span></span>

<span data-ttu-id="92388-1371">Sono supportate le app pubblicate per la distribuzione a 32 bit (x86) o a 64 bit (x64).</span><span class="sxs-lookup"><span data-stu-id="92388-1371">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="92388-1372">Distribuire un'app a 32 bit con .NET Core SDK a 32 bit (x86), a meno che l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-1372">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="92388-1373">Non richieda lo spazio indirizzi di memoria virtuale più grande disponibile per un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-1373">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="92388-1374">Non richieda le dimensioni maggiori dello stack IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1374">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="92388-1375">Non abbia dipendenze native a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-1375">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="92388-1376">Usare .NET Core SDK a 64 bit (x64) per pubblicare un'app a 64 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-1376">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="92388-1377">Un runtime a 64 bit deve essere presente nel sistema host.</span><span class="sxs-lookup"><span data-stu-id="92388-1377">A 64-bit runtime must be present on the host system.</span></span>

<span data-ttu-id="92388-1378">ASP.NET Core viene fornito con il [server Kestrel](xref:fundamentals/servers/kestrel), ovvero un server HTTP multipiattaforma predefinito.</span><span class="sxs-lookup"><span data-stu-id="92388-1378">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="92388-1379">Quando si usa [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) o [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), l'app viene eseguita in un processo separato dal processo di lavoro IIS (*out-of-process*) con il [server Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="92388-1379">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="92388-1380">Poiché le app ASP.NET Core vengono eseguite in un processo distinto dal processo di lavoro IIS, il modulo esegue la gestione dei processi.</span><span class="sxs-lookup"><span data-stu-id="92388-1380">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="92388-1381">Il modulo avvia il processo per l'app ASP.NET Core quando arriva la prima richiesta e riavvia l'app se viene arrestata o si arresta in modo anomalo.</span><span class="sxs-lookup"><span data-stu-id="92388-1381">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="92388-1382">Si tratta essenzialmente dello stesso comportamento delle app eseguite in-process e gestite dal [servizio Attivazione processo Windows](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="92388-1382">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="92388-1383">Il diagramma seguente illustra la relazione tra IIS, il modulo ASP.NET Core e un'app ospitata out-of-process:</span><span class="sxs-lookup"><span data-stu-id="92388-1383">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modulo ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="92388-1385">Le richieste arrivano dal Web al driver HTTP.sys in modalità kernel.</span><span class="sxs-lookup"><span data-stu-id="92388-1385">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="92388-1386">Il driver instrada le richieste a IIS sulla porta configurata per il sito Web, in genere 80 (HTTP) o 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="92388-1386">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="92388-1387">Il modulo inoltra le richieste a Kestrel su una porta casuale per l'app non corrispondente alla porta 80 o 443.</span><span class="sxs-lookup"><span data-stu-id="92388-1387">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="92388-1388">Il modulo specifica la porta tramite una variabile di ambiente all'avvio e il [middleware di integrazione di IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configura il server per l'ascolto `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="92388-1388">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="92388-1389">Vengono eseguiti controlli aggiuntivi e le richieste che non provengono dal modulo vengono rifiutate.</span><span class="sxs-lookup"><span data-stu-id="92388-1389">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="92388-1390">Il modulo non supporta l'inoltro HTTPS, pertanto le richieste vengono inoltrate tramite HTTP anche se sono state ricevute da IIS tramite HTTPS.</span><span class="sxs-lookup"><span data-stu-id="92388-1390">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="92388-1391">Dopo che Kestrel ha prelevato la richiesta dal modulo, viene eseguito il push della richiesta nella pipeline middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1391">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="92388-1392">La pipeline middleware gestisce la richiesta e la passa come istanza di `HttpContext` alla logica dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1392">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="92388-1393">Il middleware aggiunto dall'integrazione di IIS aggiorna lo schema, l'IP remoto e il percorso di base all'account per l'inoltro della richiesta a Kestrel.</span><span class="sxs-lookup"><span data-stu-id="92388-1393">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="92388-1394">La risposta dell'app viene quindi passata a IIS, che ne esegue di nuovo il push al client HTTP che ha avviato la richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1394">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="92388-1395">`CreateDefaultBuilder` configura il server [Kestrel](xref:fundamentals/servers/kestrel) come server Web e abilita l'integrazione di IIS configurando il percorso base e la porta per il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-1395">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="92388-1396">Il modulo ASP.NET Core genera una porta dinamica da assegnare al processo back-end.</span><span class="sxs-lookup"><span data-stu-id="92388-1396">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="92388-1397">`CreateDefaultBuilder` chiama il metodo <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>.</span><span class="sxs-lookup"><span data-stu-id="92388-1397">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="92388-1398">`UseIISIntegration` configura Kestrel per l'ascolto sulla porta dinamica all'indirizzo IP localhost (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="92388-1398">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="92388-1399">Se la porta dinamica è 1234, Kestrel è in ascolto su `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="92388-1399">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="92388-1400">Questa configurazione sostituisce le altre configurazioni URL fornite da:</span><span class="sxs-lookup"><span data-stu-id="92388-1400">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="92388-1401">API Listen di Kestrel</span><span class="sxs-lookup"><span data-stu-id="92388-1401">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="92388-1402">[Configurazione](xref:fundamentals/configuration/index) (o [opzione URL della riga di comando](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="92388-1402">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="92388-1403">Le chiamate a `UseUrls` o all'API `Listen` di Kestrel non sono necessarie quando si usa il modulo.</span><span class="sxs-lookup"><span data-stu-id="92388-1403">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="92388-1404">In caso di chiamata di `UseUrls` o `Listen`, Kestrel resta in ascolto sulla porta specificata solo quando si esegue l'app senza IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1404">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

<span data-ttu-id="92388-1405">Per indicazioni sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-1405">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="92388-1406">Per altre informazioni sull'hosting, vedere [Hosting in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="92388-1406">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="92388-1407">Configurazione dell'applicazione</span><span class="sxs-lookup"><span data-stu-id="92388-1407">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="92388-1408">Abilitare i componenti IISIntegration</span><span class="sxs-lookup"><span data-stu-id="92388-1408">Enable the IISIntegration components</span></span>

<span data-ttu-id="92388-1409">Quando si compila un host in `CreateWebHostBuilder` (*Program.cs*), chiamare <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> per abilitare l'integrazione con IIS:</span><span class="sxs-lookup"><span data-stu-id="92388-1409">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="92388-1410">Per altre informazioni su `CreateDefaultBuilder`, vedere <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="92388-1410">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

### <a name="iis-options"></a><span data-ttu-id="92388-1411">Opzioni IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1411">IIS options</span></span>

| <span data-ttu-id="92388-1412">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-1412">Option</span></span>                         | <span data-ttu-id="92388-1413">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-1413">Default</span></span> | <span data-ttu-id="92388-1414">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-1414">Setting</span></span> |
| ---
<span data-ttu-id="92388-1415">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1415">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1416">'Blazor'</span></span>
- <span data-ttu-id="92388-1417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1417">'Identity'</span></span>
- <span data-ttu-id="92388-1418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1418">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1419">'Razor'</span></span>
- <span data-ttu-id="92388-1420">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1421">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1422">'Blazor'</span></span>
- <span data-ttu-id="92388-1423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1423">'Identity'</span></span>
- <span data-ttu-id="92388-1424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1424">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1425">'Razor'</span></span>
- <span data-ttu-id="92388-1426">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1427">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1428">'Blazor'</span></span>
- <span data-ttu-id="92388-1429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1429">'Identity'</span></span>
- <span data-ttu-id="92388-1430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1430">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1431">'Razor'</span></span>
- <span data-ttu-id="92388-1432">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1433">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1434">'Blazor'</span></span>
- <span data-ttu-id="92388-1435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1435">'Identity'</span></span>
- <span data-ttu-id="92388-1436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1436">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1437">'Razor'</span></span>
- <span data-ttu-id="92388-1438">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1439">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1440">'Blazor'</span></span>
- <span data-ttu-id="92388-1441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1441">'Identity'</span></span>
- <span data-ttu-id="92388-1442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1442">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1443">'Razor'</span></span>
- <span data-ttu-id="92388-1444">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1445">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1446">'Blazor'</span></span>
- <span data-ttu-id="92388-1447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1447">'Identity'</span></span>
- <span data-ttu-id="92388-1448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1448">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1449">'Razor'</span></span>
- <span data-ttu-id="92388-1450">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1451">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1452">'Blazor'</span></span>
- <span data-ttu-id="92388-1453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1453">'Identity'</span></span>
- <span data-ttu-id="92388-1454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1454">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1455">'Razor'</span></span>
- <span data-ttu-id="92388-1456">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1457">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1458">'Blazor'</span></span>
- <span data-ttu-id="92388-1459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1459">'Identity'</span></span>
- <span data-ttu-id="92388-1460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1460">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1461">'Razor'</span></span>
- <span data-ttu-id="92388-1462">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1463">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1463">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1464">'Blazor'</span></span>
- <span data-ttu-id="92388-1465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1465">'Identity'</span></span>
- <span data-ttu-id="92388-1466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1466">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1467">'Razor'</span></span>
- <span data-ttu-id="92388-1468">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1469">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1470">'Blazor'</span></span>
- <span data-ttu-id="92388-1471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1471">'Identity'</span></span>
- <span data-ttu-id="92388-1472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1472">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1473">'Razor'</span></span>
- <span data-ttu-id="92388-1474">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1475">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1476">'Blazor'</span></span>
- <span data-ttu-id="92388-1477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1477">'Identity'</span></span>
- <span data-ttu-id="92388-1478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1478">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1479">'Razor'</span></span>
- <span data-ttu-id="92388-1480">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1480">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1481">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1481">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1482">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1482">'Blazor'</span></span>
- <span data-ttu-id="92388-1483">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1483">'Identity'</span></span>
- <span data-ttu-id="92388-1484">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1484">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1485">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1485">'Razor'</span></span>
- <span data-ttu-id="92388-1486">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1486">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1487">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1487">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1488">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1488">'Blazor'</span></span>
- <span data-ttu-id="92388-1489">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1489">'Identity'</span></span>
- <span data-ttu-id="92388-1490">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1490">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1491">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1491">'Razor'</span></span>
- <span data-ttu-id="92388-1492">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1492">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-1493">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1493">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1494">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1494">'Blazor'</span></span>
- <span data-ttu-id="92388-1495">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1495">'Identity'</span></span>
- <span data-ttu-id="92388-1496">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1496">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1497">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1497">'Razor'</span></span>
- <span data-ttu-id="92388-1498">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1498">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-1499">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il server IIS imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-1499">---- | | `AutomaticAuthentication`      | `true`  | If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-1500">Se `false`, il server fornisce solo un'identità per `HttpContext.User` e risponde alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-1500">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-1501">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1501">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-1502">Per altre informazioni, vedere [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1502">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-1503">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-1503">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> |

<span data-ttu-id="92388-1504">Per configurare le opzioni di IIS includere una configurazione del servizio per <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="92388-1504">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="92388-1505">Nell'esempio seguente si impedisce all'app di popolare `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="92388-1505">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="92388-1506">Opzione</span><span class="sxs-lookup"><span data-stu-id="92388-1506">Option</span></span>                         | <span data-ttu-id="92388-1507">Predefinito</span><span class="sxs-lookup"><span data-stu-id="92388-1507">Default</span></span> | <span data-ttu-id="92388-1508">Impostazione</span><span class="sxs-lookup"><span data-stu-id="92388-1508">Setting</span></span> |
| ---
<span data-ttu-id="92388-1509">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1509">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1510">'Blazor'</span></span>
- <span data-ttu-id="92388-1511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1511">'Identity'</span></span>
- <span data-ttu-id="92388-1512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1512">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1513">'Razor'</span></span>
- <span data-ttu-id="92388-1514">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1515">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1515">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1516">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1516">'Blazor'</span></span>
- <span data-ttu-id="92388-1517">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1517">'Identity'</span></span>
- <span data-ttu-id="92388-1518">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1518">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1519">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1519">'Razor'</span></span>
- <span data-ttu-id="92388-1520">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1520">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1521">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1521">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1522">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1522">'Blazor'</span></span>
- <span data-ttu-id="92388-1523">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1523">'Identity'</span></span>
- <span data-ttu-id="92388-1524">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1524">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1525">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1525">'Razor'</span></span>
- <span data-ttu-id="92388-1526">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1526">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1527">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1527">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1528">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1528">'Blazor'</span></span>
- <span data-ttu-id="92388-1529">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1529">'Identity'</span></span>
- <span data-ttu-id="92388-1530">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1530">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1531">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1531">'Razor'</span></span>
- <span data-ttu-id="92388-1532">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1532">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1533">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1533">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1534">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1534">'Blazor'</span></span>
- <span data-ttu-id="92388-1535">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1535">'Identity'</span></span>
- <span data-ttu-id="92388-1536">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1536">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1537">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1537">'Razor'</span></span>
- <span data-ttu-id="92388-1538">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1538">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1539">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1539">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1540">'Blazor'</span></span>
- <span data-ttu-id="92388-1541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1541">'Identity'</span></span>
- <span data-ttu-id="92388-1542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1542">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1543">'Razor'</span></span>
- <span data-ttu-id="92388-1544">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1545">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1545">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1546">'Blazor'</span></span>
- <span data-ttu-id="92388-1547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1547">'Identity'</span></span>
- <span data-ttu-id="92388-1548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1548">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1549">'Razor'</span></span>
- <span data-ttu-id="92388-1550">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1551">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1551">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1552">'Blazor'</span></span>
- <span data-ttu-id="92388-1553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1553">'Identity'</span></span>
- <span data-ttu-id="92388-1554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1554">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1555">'Razor'</span></span>
- <span data-ttu-id="92388-1556">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1557">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1557">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1558">'Blazor'</span></span>
- <span data-ttu-id="92388-1559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1559">'Identity'</span></span>
- <span data-ttu-id="92388-1560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1560">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1561">'Razor'</span></span>
- <span data-ttu-id="92388-1562">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1563">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1563">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1564">'Blazor'</span></span>
- <span data-ttu-id="92388-1565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1565">'Identity'</span></span>
- <span data-ttu-id="92388-1566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1566">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1567">'Razor'</span></span>
- <span data-ttu-id="92388-1568">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1569">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1569">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1570">'Blazor'</span></span>
- <span data-ttu-id="92388-1571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1571">'Identity'</span></span>
- <span data-ttu-id="92388-1572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1572">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1573">'Razor'</span></span>
- <span data-ttu-id="92388-1574">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1575">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1575">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1576">'Blazor'</span></span>
- <span data-ttu-id="92388-1577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1577">'Identity'</span></span>
- <span data-ttu-id="92388-1578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1578">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1579">'Razor'</span></span>
- <span data-ttu-id="92388-1580">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="92388-1581">title: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1581">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1582">'Blazor'</span></span>
- <span data-ttu-id="92388-1583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1583">'Identity'</span></span>
- <span data-ttu-id="92388-1584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1584">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1585">'Razor'</span></span>
- <span data-ttu-id="92388-1586">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1586">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-1587">--------------- | :-----: | Titolo---: autore: Descrizione: monikerRange: ms. Author: ms. Custom: ms. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="92388-1587">--------------- | :-----: | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="92388-1588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="92388-1588">'Blazor'</span></span>
- <span data-ttu-id="92388-1589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="92388-1589">'Identity'</span></span>
- <span data-ttu-id="92388-1590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="92388-1590">'Let's Encrypt'</span></span>
- <span data-ttu-id="92388-1591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="92388-1591">'Razor'</span></span>
- <span data-ttu-id="92388-1592">SignalRUID '':</span><span class="sxs-lookup"><span data-stu-id="92388-1592">'SignalR' uid:</span></span> 

<span data-ttu-id="92388-1593">---- | | `AutomaticAuthentication`      | `true`  | Se `true` , il [middleware di integrazione di IIS](#enable-the-iisintegration-components) imposta l' `HttpContext.User` autenticazione di [Windows](xref:security/authentication/windowsauth)autenticata.</span><span class="sxs-lookup"><span data-stu-id="92388-1593">---- | | `AutomaticAuthentication`      | `true`  | If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="92388-1594">Se `false`, il middleware fornisce solo un'identità per `HttpContext.User` e risponde solo alle richieste esplicite di `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="92388-1594">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="92388-1595">Per il funzionamento di `AutomaticAuthentication` l’autenticazione di Windows deve essere abilitata in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1595">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="92388-1596">Per altre informazioni, vedere l'argomento [Autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1596">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> <span data-ttu-id="92388-1597">| | `AuthenticationDisplayName`    | `null`  | Imposta il nome visualizzato degli utenti nelle pagine di accesso.</span><span class="sxs-lookup"><span data-stu-id="92388-1597">| | `AuthenticationDisplayName`    | `null`  | Sets the display name shown to users on login pages.</span></span> <span data-ttu-id="92388-1598">| | `ForwardClientCertificate`     | `true`  | Se `true` e l' `MS-ASPNETCORE-CLIENTCERT` intestazione della richiesta è presente, `HttpContext.Connection.ClientCertificate` viene popolato.</span><span class="sxs-lookup"><span data-stu-id="92388-1598">| | `ForwardClientCertificate`     | `true`  | If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="92388-1599">Scenari con server proxy e servizi di bilanciamento del carico</span><span class="sxs-lookup"><span data-stu-id="92388-1599">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="92388-1600">Il [middleware di integrazione IIS](#enable-the-iisintegration-components), che consente di configurare il middleware delle intestazioni inoltrate, e il modulo ASP.NET Core sono configurati per inoltrare lo schema (HTTP/HTTPS) e l'indirizzo IP remoto di origine della richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1600">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="92388-1601">Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro ulteriori server proxy e servizi di bilanciamento del carico.</span><span class="sxs-lookup"><span data-stu-id="92388-1601">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="92388-1602">Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="92388-1602">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="92388-1603">File web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1603">web.config file</span></span>

<span data-ttu-id="92388-1604">Il file *web.config* configura il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-1604">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-1605">La creazione, la trasformazione e la pubblicazione del file *web.config* sono operazioni gestite da una destinazione MSBuild (`_TransformWebConfig`) quando viene pubblicato il progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-1605">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="92388-1606">Questa destinazione si trova tra le destinazioni Web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="92388-1606">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="92388-1607">L'SDK è impostato all'inizio del file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-1607">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="92388-1608">Se nel progetto non è presente un file *web.config*, il file viene creato con un valore *processPath* e *argomenti* corretti per la configurazione del modulo ASP.NET Core. Il file viene quindi spostato nell'[output pubblicato](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="92388-1608">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="92388-1609">Se nel progetto è presente un file *web.config*, il file viene trasformato con il valore di *processPath* e gli *argomenti* corretti per la configurazione del modulo ASP.NET Core, quindi viene spostato nell'output pubblicato.</span><span class="sxs-lookup"><span data-stu-id="92388-1609">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="92388-1610">La trasformazione non modifica le impostazioni di configurazione di IIS incluse nel file.</span><span class="sxs-lookup"><span data-stu-id="92388-1610">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="92388-1611">Il file *web.config* può fornire ulteriori impostazioni di configurazione di IIS che controllano i moduli IIS attivi.</span><span class="sxs-lookup"><span data-stu-id="92388-1611">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="92388-1612">Per informazioni sui moduli IIS in grado di elaborare le richieste con le app di ASP.NET Core, vedere l'argomento [Moduli IIS](xref:host-and-deploy/iis/modules).</span><span class="sxs-lookup"><span data-stu-id="92388-1612">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="92388-1613">Per impedire a Web SDK di trasformare il file *Web. config* , utilizzare la **\<IsTransformWebConfigDisabled>** proprietà nel file di progetto:</span><span class="sxs-lookup"><span data-stu-id="92388-1613">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="92388-1614">Quando si disabilita la trasformazione del file in Web SDK, il valore di *processPath* e gli *argomenti* devono essere impostati manualmente dallo sviluppatore.</span><span class="sxs-lookup"><span data-stu-id="92388-1614">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="92388-1615">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="92388-1615">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="92388-1616">Posizione del file web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1616">web.config file location</span></span>

<span data-ttu-id="92388-1617">Per configurare correttamente il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , il file *Web. config* deve essere presente nel percorso [radice del contenuto](xref:fundamentals/index#content-root) (in genere il percorso di base dell'app) dell'app distribuita.</span><span class="sxs-lookup"><span data-stu-id="92388-1617">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="92388-1618">Corrisponde al percorso fisico del sito Web fornito a IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1618">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="92388-1619">Il file *web.config* deve essere presente nella radice dell'app per abilitare la pubblicazione di più app mediante Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-1619">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="92388-1620">I file sensibili sono presenti nel percorso fisico dell'app, ad esempio \* \<assembly> . runtimeconfig. JSON*, \* \<assembly> . XML* (commenti in formato documentazione XML) e \* \<assembly> . Deps. JSON\*.</span><span class="sxs-lookup"><span data-stu-id="92388-1620">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="92388-1621">Quando il file *web.config* è presente e il sito viene avviato normalmente, IIS non rende disponibili questi file riservati se vengono richiesti.</span><span class="sxs-lookup"><span data-stu-id="92388-1621">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="92388-1622">Se il file *web.config* non è presente, ha un nome non corretto o non è in grado di configurare il sito per l'avvio normale, IIS potrebbe rendere disponibili pubblicamente i file riservati.</span><span class="sxs-lookup"><span data-stu-id="92388-1622">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="92388-1623">**Il file *Web. config* deve essere sempre presente nella distribuzione, denominato correttamente e in grado di configurare il sito per l'avvio normale. Non rimuovere mai il file *Web. config* da una distribuzione di produzione.**</span><span class="sxs-lookup"><span data-stu-id="92388-1623">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="92388-1624">Trasformare web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1624">Transform web.config</span></span>

<span data-ttu-id="92388-1625">Se è necessario trasformare *web.config* in fase di pubblicazione (ad esempio, impostare variabili di ambiente in base a configurazione, profilo o ambiente), vedere <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="92388-1625">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="92388-1626">Configurazione di IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1626">IIS configuration</span></span>

<span data-ttu-id="92388-1627">**Sistemi operativi Windows Server**</span><span class="sxs-lookup"><span data-stu-id="92388-1627">**Windows Server operating systems**</span></span>

<span data-ttu-id="92388-1628">Abilitare il ruolo del server **Server Web (IIS)** e stabilire i servizi di ruolo.</span><span class="sxs-lookup"><span data-stu-id="92388-1628">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="92388-1629">Usare la procedura guidata **Aggiungi ruoli e funzionalità** accessibile tramite il menu **Gestisci** o il collegamento in **Server Manager**.</span><span class="sxs-lookup"><span data-stu-id="92388-1629">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="92388-1630">Nel passaggio **Ruoli del server** selezionare la casella per **Server Web (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="92388-1630">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Il ruolo Server Web IIS viene selezionato nel passaggio dei ruoli del server Selezionare.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="92388-1632">Dopo il passaggio **Funzionalità**, viene caricato il passaggio**Servizi ruolo** per Server Web (IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-1632">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="92388-1633">Selezionare i servizi ruolo IIS desiderati o accettare i servizi ruolo predefiniti specificati.</span><span class="sxs-lookup"><span data-stu-id="92388-1633">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![I servizi ruolo predefiniti vengono selezionati nel passaggio Selezionare i servizi ruolo.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="92388-1635">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1635">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-1636">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: sicurezza del **server Web**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-1636">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="92388-1637">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-1637">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-1638">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1638">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-1639">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-1639">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-1640">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-1640">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-1641">Per abilitare i WebSocket, espandere i nodi seguenti: sviluppo di applicazioni **server Web**  >  **Application Development**.</span><span class="sxs-lookup"><span data-stu-id="92388-1641">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="92388-1642">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-1642">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-1643">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-1643">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-1644">Procedere con il passaggio **Conferma** per installare il ruolo del server web e i servizi.</span><span class="sxs-lookup"><span data-stu-id="92388-1644">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="92388-1645">Dopo l'installazione del ruolo **server Web (IIS)** non è necessario riavviare il server o IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1645">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="92388-1646">**Sistemi operativi desktop di Windows**</span><span class="sxs-lookup"><span data-stu-id="92388-1646">**Windows desktop operating systems**</span></span>

<span data-ttu-id="92388-1647">Abilitare **Console di gestione IIS** e **Servizi Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1647">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-1648">Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità** > **Disattivare o attivare le funzionalità di Windows** (a sinistra dello schermo).</span><span class="sxs-lookup"><span data-stu-id="92388-1648">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="92388-1649">Aprire il nodo **Internet Information Services**.</span><span class="sxs-lookup"><span data-stu-id="92388-1649">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="92388-1650">Aprire il nodo **Strumenti di gestione Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1650">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="92388-1651">Selezionare la casella per **Console di gestione IIS**.</span><span class="sxs-lookup"><span data-stu-id="92388-1651">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="92388-1652">Selezionare la casella per **Servizi World Wide Web**.</span><span class="sxs-lookup"><span data-stu-id="92388-1652">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="92388-1653">Accettare le funzionalità predefinite per **Servizi World Wide Web** o personalizzare le funzionalità IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1653">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="92388-1654">**Autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1654">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="92388-1655">Per abilitare l'autenticazione di Windows, espandere i nodi seguenti: **World Wide Web sicurezza dei servizi**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="92388-1655">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="92388-1656">Selezionare la funzionalità **Autenticazione di Windows**.</span><span class="sxs-lookup"><span data-stu-id="92388-1656">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="92388-1657">Per ulteriori informazioni, vedere [ \<windowsAuthentication> autenticazione di Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) e [configurare l'autenticazione di Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1657">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="92388-1658">**WebSockets (facoltativo)**</span><span class="sxs-lookup"><span data-stu-id="92388-1658">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="92388-1659">WebSockets è supportato con ASP.NET Core 1.1 o versioni successive.</span><span class="sxs-lookup"><span data-stu-id="92388-1659">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="92388-1660">Per abilitare i WebSocket, espandere i nodi seguenti: funzionalità di sviluppo di applicazioni di **World Wide Web Services**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="92388-1660">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="92388-1661">Selezionare la funzionalità **Protocollo WebSocket**.</span><span class="sxs-lookup"><span data-stu-id="92388-1661">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="92388-1662">Per altre informazioni, vedere [Oggetti WebSocket](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="92388-1662">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="92388-1663">Se l'installazione di IIS richiede un riavvio, riavviare il sistema.</span><span class="sxs-lookup"><span data-stu-id="92388-1663">If the IIS installation requires a restart, restart the system.</span></span>

![La console di gestione IIS e servizi World Wide Web sono selezionati nelle funzionalità di Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="92388-1665">Installare il bundle di hosting .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-1665">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="92388-1666">Installare il *bundle di hosting .NET Core* nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1666">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="92388-1667">Il bundle installa il runtime di .NET Core, la libreria di .NET Core e il [modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="92388-1667">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="92388-1668">Il modulo consente l'esecuzione delle app ASP.NET Core dietro IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1668">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92388-1669">Se il bundle di hosting viene installato prima di IIS, è necessario riparare l'installazione del bundle.</span><span class="sxs-lookup"><span data-stu-id="92388-1669">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="92388-1670">Eseguire di nuovo il programma di installazione del bundle di hosting dopo l'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1670">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="92388-1671">Se il bundle di hosting viene installato dopo l'installazione della versione a 64 bit (x64) di .NET Core, uno o più SDK potrebbero risultare mancanti ([Non sono stati rilevati .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="92388-1671">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="92388-1672">Per risolvere il problema, vedere <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="92388-1672">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="download"></a><span data-ttu-id="92388-1673">Download</span><span class="sxs-lookup"><span data-stu-id="92388-1673">Download</span></span>

1. <span data-ttu-id="92388-1674">Passare alla pagina [download di .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="92388-1674">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="92388-1675">Selezionare la versione desiderata di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1675">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="92388-1676">Nella colonna **Run apps - Runtime** (App di esecuzione - Runtime), trovare la riga della versione di runtime di .NET Core desiderata.</span><span class="sxs-lookup"><span data-stu-id="92388-1676">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="92388-1677">Scaricare il programma di installazione usando il collegamento **bundle di hosting** .</span><span class="sxs-lookup"><span data-stu-id="92388-1677">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="92388-1678">Alcuni programmi di installazione contengono versioni che hanno terminato il loro ciclo di vita e non sono più supportate da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="92388-1678">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="92388-1679">Per altre informazioni, vedere i [criteri di supporto](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="92388-1679">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="92388-1680">Installare il bundle di hosting</span><span class="sxs-lookup"><span data-stu-id="92388-1680">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="92388-1681">Eseguire il programma di installazione nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-1681">Run the installer on the server.</span></span> <span data-ttu-id="92388-1682">Quando si esegue il programma di installazione da una shell dei comandi di amministratore sono disponibili i parametri seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1682">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="92388-1683">`OPT_NO_ANCM=1`: Ignorare l'installazione del modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1683">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="92388-1684">`OPT_NO_RUNTIME=1`: Ignorare l'installazione del runtime di .NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1684">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="92388-1685">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-1685">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-1686">`OPT_NO_SHAREDFX=1`: Ignora l'installazione del Framework condiviso ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="92388-1686">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="92388-1687">Utilizzato quando il server ospita solo le [distribuzioni autosufficienti (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="92388-1687">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="92388-1688">`OPT_NO_X86=1`: Ignora l'installazione di Runtime x86.</span><span class="sxs-lookup"><span data-stu-id="92388-1688">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="92388-1689">Usare questo parametro se si è certi che non verrà eseguito l'hosting di app a 32 bit.</span><span class="sxs-lookup"><span data-stu-id="92388-1689">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="92388-1690">Se non esiste alcuna possibilità che in futuro venga eseguito l'hosting di app sia a 32 che a 64 bit, non usare questo parametro e installare entrambi i runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-1690">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="92388-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disabilitare il controllo per l'utilizzo di una configurazione condivisa di IIS quando la configurazione condivisa (*ApplicationHost. config*) si trova nello stesso computer dell'installazione di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1691">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="92388-1692">*Disponibile solo per i programmi di installazione di bundler di hosting ASP.NET Core 2.2 o versioni successive.*</span><span class="sxs-lookup"><span data-stu-id="92388-1692">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="92388-1693">Per altre informazioni, vedere <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="92388-1693">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="92388-1694">Riavviare il sistema o eseguire i comandi seguenti in una shell dei comandi:</span><span class="sxs-lookup"><span data-stu-id="92388-1694">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="92388-1695">Il riavvio di IIS rileva una modifica alla variabile di ambiente di sistema PATH apportata dal programma di installazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1695">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="92388-1696">Non è necessario arrestare manualmente singoli siti in IIS durante l'installazione del bundle di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1696">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="92388-1697">Le app ospitate (siti IIS) vengono riavviate quando IIS viene riavviato.</span><span class="sxs-lookup"><span data-stu-id="92388-1697">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="92388-1698">Le app vengono riavviate quando ricevono la prima richiesta, incluso il [modulo di inizializzazione dell'applicazione](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="92388-1698">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="92388-1699">ASP.NET Core adotta il comportamento di rollforward per le versioni di patch dei pacchetti di Framework condivisi.</span><span class="sxs-lookup"><span data-stu-id="92388-1699">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="92388-1700">Quando le app ospitate da IIS vengono riavviate con IIS, le app vengono caricate con le ultime versioni di patch dei pacchetti a cui si fa riferimento quando ricevono la prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1700">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="92388-1701">Se IIS non viene riavviato, le app vengono riavviate e presentano un comportamento di rollforward quando i processi di lavoro vengono riciclati e ricevono la prima richiesta.</span><span class="sxs-lookup"><span data-stu-id="92388-1701">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

> [!NOTE]
> <span data-ttu-id="92388-1702">Per informazioni sulla configurazione condivisa di IIS, vedere [Modulo di ASP.NET Core con configurazione condivisa di IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="92388-1702">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="92388-1703">Installare la Distribuzione Web quando si pubblica con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1703">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="92388-1704">Quando si distribuiscono app ai server con [Distribuzione Web](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installare la versione più recente di Distribuzione Web nel server.</span><span class="sxs-lookup"><span data-stu-id="92388-1704">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="92388-1705">Per installare Distribuzione Web usare l'[Installazione guidata piattaforma Web (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) o ottenere direttamente un programma di installazione in [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="92388-1705">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="92388-1706">Il metodo preferito consiste nell'usare WebPI.</span><span class="sxs-lookup"><span data-stu-id="92388-1706">The preferred method is to use WebPI.</span></span> <span data-ttu-id="92388-1707">WebPI offre un'installazione autonoma e una configurazione per i provider di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1707">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="92388-1708">Creare il sito IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1708">Create the IIS site</span></span>

1. <span data-ttu-id="92388-1709">Nel sistema host creare una cartella per contenere le cartelle e i file pubblicati dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1709">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="92388-1710">In un passaggio successivo, il percorso della cartella viene fornito a IIS come percorso fisico dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1710">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="92388-1711">Per altre informazioni sulla cartella di distribuzione e il layout dei file di un'app, vedere <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="92388-1711">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="92388-1712">In Gestione IIS aprire il nodo del server nel pannello **connessioni** .</span><span class="sxs-lookup"><span data-stu-id="92388-1712">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="92388-1713">Fare clic con il pulsante destro del mouse sulla cartella **Siti**.</span><span class="sxs-lookup"><span data-stu-id="92388-1713">Right-click the **Sites** folder.</span></span> <span data-ttu-id="92388-1714">Scegliere **Aggiungi sito Web** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-1714">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-1715">Specificare un **Nome del sito** e impostare il **Percorso fisico** per la cartella di distribuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1715">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="92388-1716">Specificare la configurazione dell' **associazione** e creare il sito Web selezionando **OK**:</span><span class="sxs-lookup"><span data-stu-id="92388-1716">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Specificare il nome del sito, il percorso fisico e il nome Host nel passaggio Aggiungi sito Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="92388-1718">Le associazioni con caratteri jolly di livello superiore (`http://*:80/` e `http://+:80`) **non** devono essere usate,</span><span class="sxs-lookup"><span data-stu-id="92388-1718">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="92388-1719">poiché possono introdurre vulnerabilità a livello di sicurezza nell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1719">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="92388-1720">Questo concetto vale sia per i caratteri jolly sicuri che vulnerabili.</span><span class="sxs-lookup"><span data-stu-id="92388-1720">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="92388-1721">Usare nomi host espliciti al posto di caratteri jolly.</span><span class="sxs-lookup"><span data-stu-id="92388-1721">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="92388-1722">L'associazione con caratteri jolly del sottodominio (ad esempio, `*.mysub.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile).</span><span class="sxs-lookup"><span data-stu-id="92388-1722">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="92388-1723">Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1723">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="92388-1724">Nel nodo del server selezionare **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1724">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="92388-1725">Fare clic con il pulsante destro del mouse sul pool di applicazioni del sito e scegliere **Impostazioni di base** dal menu di scelta rapida.</span><span class="sxs-lookup"><span data-stu-id="92388-1725">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="92388-1726">Nella finestra **Modifica pool di applicazioni** impostare **Versione .NET CLR** su **Nessun codice gestito**.</span><span class="sxs-lookup"><span data-stu-id="92388-1726">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Impostare Nessun codice gestito per la versione .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="92388-1728">ASP.NET Core viene eseguito in un processo separato e gestisce il runtime.</span><span class="sxs-lookup"><span data-stu-id="92388-1728">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="92388-1729">ASP.NET Core non si basa sul caricamento di Common Language Runtime (CLR di .NET) desktop. Core Common Language Runtime (CoreCLR) for .NET Core viene avviato per ospitare l'app nel processo di lavoro.</span><span class="sxs-lookup"><span data-stu-id="92388-1729">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="92388-1730">L'impostazione di **Versione .NET CLR** su **Nessun codice gestito** è facoltativa ma consigliata.</span><span class="sxs-lookup"><span data-stu-id="92388-1730">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="92388-1731">*ASP.NET Core 2.2 o versioni successive*: per una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) a 64 bit (x64) che usa il [modello di hosting In-Process](#in-process-hosting-model), disabilitare il pool di app per i processi a 32 bit (x86).</span><span class="sxs-lookup"><span data-stu-id="92388-1731">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="92388-1732">Nella barra laterale **Azioni** in Gestione IIS > **Pool di applicazioni** selezionare **Impostazioni predefinite pool di applicazioni** o **Impostazioni avanzate**.</span><span class="sxs-lookup"><span data-stu-id="92388-1732">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="92388-1733">Individuare **Attiva applicazioni a 32 bit** e impostare il valore su `False`.</span><span class="sxs-lookup"><span data-stu-id="92388-1733">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="92388-1734">Questa impostazione non viene applicata alle app distribuite per l'[hosting out-of-process](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="92388-1734">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="92388-1735">Confermare che l'identità del modello del processo disponga delle autorizzazioni appropriate.</span><span class="sxs-lookup"><span data-stu-id="92388-1735">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="92388-1736">Se l'identità predefinita del pool di app (**modello**  >  **Identity** di processo) viene modificata da **ApplicationPoolIdentity** a un'altra identità, verificare che la nuova identità disponga delle autorizzazioni necessarie per accedere alla cartella dell'app, al database e ad altre risorse richieste.</span><span class="sxs-lookup"><span data-stu-id="92388-1736">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="92388-1737">Ad esempio, il pool di applicazioni richiede l'accesso in lettura e scrittura alle cartelle in cui l'app legge e scrive i file.</span><span class="sxs-lookup"><span data-stu-id="92388-1737">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="92388-1738">**Configurazione dell'autenticazione di Windows (facoltativa)**</span><span class="sxs-lookup"><span data-stu-id="92388-1738">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="92388-1739">Per altre informazioni, vedere [Configurare l'autenticazione Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="92388-1739">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="92388-1740">Distribuire l'app</span><span class="sxs-lookup"><span data-stu-id="92388-1740">Deploy the app</span></span>

<span data-ttu-id="92388-1741">Distribuire l'app nella cartella **Percorso fisico** di IIS specificata nella sezione [Creare il sito IIS](#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="92388-1741">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="92388-1742">[Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) è il meccanismo consigliato per la distribuzione, ma esistono diverse opzioni per spostare l'app dalla cartella di *pubblicazione* del progetto alla cartella di distribuzione del sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1742">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="92388-1743">Distribuzione web con Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1743">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="92388-1744">Vedere l'argomento [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) (Profili di pubblicazione Visual Studio per la distribuzione di app ASP.NET Core) per informazioni su come creare un profilo di pubblicazione da usare con Distribuzione Web.</span><span class="sxs-lookup"><span data-stu-id="92388-1744">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="92388-1745">Se il provider di hosting fornisce un profilo di pubblicazione o il supporto per crearne uno, scaricare il profilo e importarlo usando la finestra di dialogo **Pubblica** di Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="92388-1745">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Pagina della finestra di dialogo Pubblica](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="92388-1747">Distribuzione Web all'esterno di Visual Studio</span><span class="sxs-lookup"><span data-stu-id="92388-1747">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="92388-1748">È anche possibile usare [Distribuzione Web](/iis/publish/using-web-deploy/introduction-to-web-deploy) all'esterno di Visual Studio dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="92388-1748">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="92388-1749">Per altre informazioni sulla distribuzione, vedere [Strumento Distribuzione Web](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="92388-1749">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="92388-1750">Alternative a Distribuzione web</span><span class="sxs-lookup"><span data-stu-id="92388-1750">Alternatives to Web Deploy</span></span>

<span data-ttu-id="92388-1751">Usare uno dei metodi disponibili, ad esempio la copia manuale, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) o [PowerShell](/powershell/), per spostare l'app nel sistema di hosting.</span><span class="sxs-lookup"><span data-stu-id="92388-1751">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="92388-1752">Per altre informazioni sulla distribuzione di ASP.NET Core in IIS, vedere la sezione [Risorse di distribuzione per amministratori IIS](#deployment-resources-for-iis-administrators).</span><span class="sxs-lookup"><span data-stu-id="92388-1752">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="92388-1753">Esplorare il sito Web</span><span class="sxs-lookup"><span data-stu-id="92388-1753">Browse the website</span></span>

<span data-ttu-id="92388-1754">Dopo aver distribuito l'app nel sistema di hosting, effettuare una richiesta a uno degli endpoint pubblici dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1754">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="92388-1755">Nell'esempio seguente il sito è associato al **nome host** di IIS `www.mysite.com` sulla **porta** `80`.</span><span class="sxs-lookup"><span data-stu-id="92388-1755">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="92388-1756">Viene effettuata una richiesta a `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="92388-1756">A request is made to `http://www.mysite.com`:</span></span>

![Il browser Microsoft Edge ha caricato la pagina di avvio IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="92388-1758">File di distribuzione bloccati</span><span class="sxs-lookup"><span data-stu-id="92388-1758">Locked deployment files</span></span>

<span data-ttu-id="92388-1759">I file nella cartella di distribuzione sono bloccati durante l'esecuzione dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1759">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="92388-1760">I file bloccati non possono essere sovrascritti durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-1760">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="92388-1761">Per rilasciare i file bloccati in una distribuzione, arrestare il pool di applicazioni usando **uno** dei metodi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1761">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-1762">Usare Distribuzione Web e fare riferimento a `Microsoft.NET.Sdk.Web` nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="92388-1762">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="92388-1763">Nella radice della directory dell'app web viene posizionato un file *app_offline.htm*.</span><span class="sxs-lookup"><span data-stu-id="92388-1763">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="92388-1764">Quando il file è presente, il modulo ASP.NET Core arresta normalmente l'app e rende disponibile il file *app_offline.htm* durante la distribuzione.</span><span class="sxs-lookup"><span data-stu-id="92388-1764">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="92388-1765">Per altre informazioni, vedere la [Guida di riferimento per la configurazione del modulo ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="92388-1765">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="92388-1766">Arrestare manualmente il pool di applicazioni in Gestione IIS sul server.</span><span class="sxs-lookup"><span data-stu-id="92388-1766">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="92388-1767">Usare PowerShell per eliminare *App_offline. htm* (richiede PowerShell 5 o versione successiva):</span><span class="sxs-lookup"><span data-stu-id="92388-1767">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="92388-1768">Protezione dei dati</span><span class="sxs-lookup"><span data-stu-id="92388-1768">Data protection</span></span>

<span data-ttu-id="92388-1769">Lo [stack di protezione dei dati di ASP.NET Core](xref:security/data-protection/introduction) viene usato da diversi [middleware](xref:fundamentals/middleware/index) di ASP.NET Core, inclusi quelli usati nell'autenticazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1769">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="92388-1770">Anche se le DPAPI (Data Protection API) non vengono chiamate dal codice dell'utente, è consigliabile configurare la protezione dati per la creazione di un [archivio di chiavi](xref:security/data-protection/implementation/key-management) crittografiche permanente con uno script di distribuzione o nel codice dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1770">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="92388-1771">Se non si configura la protezione dei dati, le chiavi vengono mantenute in memoria ed eliminate al riavvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1771">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="92388-1772">Se il gruppo di chiavi viene archiviato in memoria quando l'app viene riavviata:</span><span class="sxs-lookup"><span data-stu-id="92388-1772">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="92388-1773">Tutti i token di autenticazione basati su cookie vengono invalidati.</span><span class="sxs-lookup"><span data-stu-id="92388-1773">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="92388-1774">Gli utenti devono ripetere l'accesso alla richiesta successiva.</span><span class="sxs-lookup"><span data-stu-id="92388-1774">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="92388-1775">Tutti i dati protetti con il gruppo di chiavi non possono più essere decrittografati.</span><span class="sxs-lookup"><span data-stu-id="92388-1775">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="92388-1776">Questo può includere i [token CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) e i [cookie TEMPDATA di ASP.NET Core MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="92388-1776">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="92388-1777">Per configurare la protezione dati in IIS in modo da rendere permanente il gruppo di chiavi, usare **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1777">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="92388-1778">**Creare chiavi del Registro di sistema di protezione dati**</span><span class="sxs-lookup"><span data-stu-id="92388-1778">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="92388-1779">Le chiavi di protezione dati usate dalle app ASP.NET Core vengono archiviate nel Registro di sistema esterno alle app.</span><span class="sxs-lookup"><span data-stu-id="92388-1779">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="92388-1780">Per mantenere le chiavi per una determinata app, creare chiavi del Registro di sistema per il pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1780">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="92388-1781">Per le installazioni IIS autonome non web farm è possibile usare lo [script PowerShell Data Protection Provision-AutoGenKeys.ps1 (Provisioning di protezione dati-AutoGenKeys.ps1)](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) per ogni pool di app usato con un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1781">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="92388-1782">Questo script crea una chiave del Registro di sistema nel registro HKLM accessibile solo all'account del processo di lavoro del pool di applicazioni dell'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1782">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="92388-1783">Le chiavi vengono crittografate a riposo tramite DPAPI con una chiave a livello del computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1783">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="92388-1784">In scenari di web farm un'app può essere configurata in modo da usare un percorso UNC in cui archiviare il proprio gruppo di chiavi di protezione dati.</span><span class="sxs-lookup"><span data-stu-id="92388-1784">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="92388-1785">Per impostazione predefinita, le chiavi di protezione dati non vengono crittografate.</span><span class="sxs-lookup"><span data-stu-id="92388-1785">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="92388-1786">Assicurarsi che le autorizzazioni file per la condivisione di rete siano limitate all'account di Windows in cui viene eseguita l'app.</span><span class="sxs-lookup"><span data-stu-id="92388-1786">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="92388-1787">È possibile usare un certificato X509 per la protezione delle chiavi inattive.</span><span class="sxs-lookup"><span data-stu-id="92388-1787">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="92388-1788">Considerare l'implementazione di un meccanismo per consentire agli utenti di caricare i certificati: inserire i certificati nell'archivio certificati attendibili dell'utente e assicurarsi che siano disponibili in tutti i computer in cui viene eseguita l'app dell'utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1788">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="92388-1789">Vedere [Configurare la protezione dati di ASP.NET Core](xref:security/data-protection/configuration/overview) per altri dettagli.</span><span class="sxs-lookup"><span data-stu-id="92388-1789">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="92388-1790">**Configurare il pool di applicazioni IIS per caricare il profilo utente**</span><span class="sxs-lookup"><span data-stu-id="92388-1790">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="92388-1791">Questa impostazione si trova nella sezione **Modello del processo** in **Impostazioni avanzate** per il pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-1791">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="92388-1792">Impostare **Caricamento del profilo utente** su `True`.</span><span class="sxs-lookup"><span data-stu-id="92388-1792">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="92388-1793">Con l'impostazione `True` le chiavi vengono archiviate nella directory del profilo utente e protette tramite DPAPI con una chiave specifica per l'account utente.</span><span class="sxs-lookup"><span data-stu-id="92388-1793">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="92388-1794">Le chiavi vengono salvate in modo permanente nella cartella *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="92388-1794">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="92388-1795">Deve essere abilitato anche l'[attributo setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) del pool di app.</span><span class="sxs-lookup"><span data-stu-id="92388-1795">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="92388-1796">Il valore predefinito di `setProfileEnvironment` è `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-1796">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="92388-1797">In alcuni scenari (ad esempio, per il sistema operativo Windows), `setProfileEnvironment` è impostato su `false`.</span><span class="sxs-lookup"><span data-stu-id="92388-1797">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="92388-1798">Se le chiavi non vengono archiviate nella directory del profilo utente come previsto:</span><span class="sxs-lookup"><span data-stu-id="92388-1798">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="92388-1799">Passare alla cartella *%windir%/system32/inetsrv/config*.</span><span class="sxs-lookup"><span data-stu-id="92388-1799">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="92388-1800">Aprire il file *applicationHost.config*.</span><span class="sxs-lookup"><span data-stu-id="92388-1800">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="92388-1801">Individuare l'elemento `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="92388-1801">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="92388-1802">Verificare che l'attributo `setProfileEnvironment` non sia presente, condizione che corrisponde all'impostazione predefinita `true`, o impostare in modo esplicito il valore dell'attributo su `true`.</span><span class="sxs-lookup"><span data-stu-id="92388-1802">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="92388-1803">**Usare il file system come archivio del gruppo di chiavi**</span><span class="sxs-lookup"><span data-stu-id="92388-1803">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="92388-1804">Modificare il codice dell'app per [usare il file system come archivio del gruppo di chiavi](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-1804">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="92388-1805">Usare un certificato X509 per proteggere il gruppo di chiavi e verificare che sia un certificato attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-1805">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="92388-1806">Se il certificato è autofirmato, inserirlo nell'archivio radice attendibile.</span><span class="sxs-lookup"><span data-stu-id="92388-1806">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="92388-1807">Quando si usa IIS in una web farm:</span><span class="sxs-lookup"><span data-stu-id="92388-1807">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="92388-1808">Usare una condivisione di file a cui possono accedere tutti i computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1808">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="92388-1809">Distribuire un certificato X509 in ogni computer.</span><span class="sxs-lookup"><span data-stu-id="92388-1809">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="92388-1810">Configurare [protezione dei dati nel codice](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="92388-1810">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="92388-1811">**Impostare criteri a livello di computer per la protezione dei dati**</span><span class="sxs-lookup"><span data-stu-id="92388-1811">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="92388-1812">Il sistema di protezione dei dati ha un supporto limitato per l'impostazione di [criteri a livello di computer](xref:security/data-protection/configuration/machine-wide-policy) predefiniti per tutte le app che usano le API di protezione dei dati.</span><span class="sxs-lookup"><span data-stu-id="92388-1812">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="92388-1813">Per altre informazioni, vedere <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="92388-1813">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="92388-1814">Directory virtuali</span><span class="sxs-lookup"><span data-stu-id="92388-1814">Virtual Directories</span></span>

<span data-ttu-id="92388-1815">Le [directory virtuali IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) non sono supportate con le app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1815">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="92388-1816">Un'app può essere ospitata come [applicazione secondaria](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="92388-1816">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="92388-1817">Applicazioni secondarie</span><span class="sxs-lookup"><span data-stu-id="92388-1817">Sub-applications</span></span>

<span data-ttu-id="92388-1818">Un'app ASP.NET Core può essere ospitata come [applicazione secondaria di IIS (app secondaria)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="92388-1818">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="92388-1819">Il percorso dell'app secondaria diventa parte dell'URL dell'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-1819">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="92388-1820">Un'app secondaria non deve includere il modulo ASP.NET Core come gestore.</span><span class="sxs-lookup"><span data-stu-id="92388-1820">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="92388-1821">Se il modulo viene aggiunto come gestore nel file *Web. config* di un'applicazione secondaria, viene ricevuto un *errore del server interno 500,19* che fa riferimento al file di configurazione difettoso durante il tentativo di esplorare la sottoapp.</span><span class="sxs-lookup"><span data-stu-id="92388-1821">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="92388-1822">L'esempio seguente illustra un file *web.config* pubblicato per un'app secondaria di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="92388-1822">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="92388-1823">Quando si ospita un'app secondaria non ASP.NET Core in un'app ASP.NET Core, è necessario rimuovere in modo esplicito il gestore ereditato nel file *web.config* dell'app secondaria:</span><span class="sxs-lookup"><span data-stu-id="92388-1823">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="92388-1824">I collegamenti ad asset statici all'interno dell'app secondaria devono usare la notazione con tilde-barra (`~/`).</span><span class="sxs-lookup"><span data-stu-id="92388-1824">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="92388-1825">La notazione con tilde-barra attiva un [helper tag](xref:mvc/views/tag-helpers/intro) per anteporre la base del percorso dell'app secondaria al collegamento relativo sottoposto a rendering.</span><span class="sxs-lookup"><span data-stu-id="92388-1825">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="92388-1826">Per un'app secondaria in `/subapp_path`, il rendering di un'immagine collegata con `src="~/image.png"` viene eseguito come `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="92388-1826">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="92388-1827">Il middleware dei file statici dell'app radice non elabora la richiesta di file statici.</span><span class="sxs-lookup"><span data-stu-id="92388-1827">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="92388-1828">La richiesta viene elaborata dal middleware dei file statici dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1828">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="92388-1829">Se l'attributo `src` di un asset statico viene impostato su un percorso assoluto (ad esempio, `src="/image.png"`), il rendering del collegamento viene eseguito senza la base del percorso dell'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1829">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="92388-1830">Il middleware dei file statici dell'app radice prova a servire l'asset dalla [radice Web](xref:fundamentals/index#web-root) dell'app radice con conseguente risposta *404 - Non trovato*, a meno che l'asset statico non sia disponibile dal'app radice.</span><span class="sxs-lookup"><span data-stu-id="92388-1830">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="92388-1831">Per ospitare un'app ASP.NET Core come app secondaria in un'altra app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="92388-1831">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="92388-1832">Definire un pool di app per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1832">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="92388-1833">Impostare **Versione .NET CLR** su **Nessun codice gestito** perché Core Common Language Runtime (CoreCLR) for .NET Core viene avviato per ospitare l'app nel processo di lavoro e non CLR desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="92388-1833">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="92388-1834">Aggiungere il sito radice in Gestione IIS con l'applicazione secondaria in una cartella all'interno del sito principale.</span><span class="sxs-lookup"><span data-stu-id="92388-1834">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="92388-1835">Fare clic con il pulsante destro del mouse sulla cartella dell'applicazione secondaria in Gestione IIS e scegliere **Converti in applicazione**.</span><span class="sxs-lookup"><span data-stu-id="92388-1835">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="92388-1836">Nella finestra di dialogo **Aggiungi applicazione** usare il pulsante **Seleziona** per **Pool di applicazioni** per assegnare il pool di app creato per l'app secondaria.</span><span class="sxs-lookup"><span data-stu-id="92388-1836">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="92388-1837">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1837">Select **OK**.</span></span>

<span data-ttu-id="92388-1838">L'assegnazione di un pool di app separato all'app secondaria è un requisito quando si usa il modello di hosting in-process.</span><span class="sxs-lookup"><span data-stu-id="92388-1838">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="92388-1839">Per ulteriori informazioni sul modello di hosting in-process e sulla configurazione del modulo ASP.NET Core, vedere <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="92388-1839">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="92388-1840">Configurazione di IIS con web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1840">Configuration of IIS with web.config</span></span>

<span data-ttu-id="92388-1841">La configurazione di IIS è influenzata dalla sezione `<system.webServer>` di *web.config* per gli scenari IIS funzionali per le app ASP.NET Core con il modulo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1841">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="92388-1842">Ad esempio, la configurazione di IIS è funzionale per la compressione dinamica.</span><span class="sxs-lookup"><span data-stu-id="92388-1842">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="92388-1843">Se IIS è configurato a livello di server per l'uso della compressione dinamica, l'elemento `<urlCompression>` nel file *web.config* dell'app può disabilitare questa impostazione per un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="92388-1843">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="92388-1844">Per altre informazioni, vedere gli argomenti seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1844">For more information, see the following topics:</span></span>

* [<span data-ttu-id="92388-1845">Riferimento alla configurazione per\<system.webServer></span><span class="sxs-lookup"><span data-stu-id="92388-1845">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="92388-1846">Per impostare le variabili di ambiente per singole app in esecuzione in pool di applicazioni isolate (supportate per IIS 10,0 o versioni successive), vedere la sezione *comando appcmd. exe* dell'argomento [ \<environmentVariables> variabili di ambiente](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) nella documentazione di riferimento di IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1846">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="92388-1847">Sezioni di configurazione di web.config</span><span class="sxs-lookup"><span data-stu-id="92388-1847">Configuration sections of web.config</span></span>

<span data-ttu-id="92388-1848">Le sezioni di configurazione di app ASP.NET 4.x in *web.config* non vengono usate dalle app ASP.NET Core per la configurazione:</span><span class="sxs-lookup"><span data-stu-id="92388-1848">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="92388-1849">Le applicazioni ASP.NET Core vengono configurate tramite altri provider di configurazione.</span><span class="sxs-lookup"><span data-stu-id="92388-1849">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="92388-1850">Per ulteriori informazioni, vedere [configurazione](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="92388-1850">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="92388-1851">Pool di applicazioni</span><span class="sxs-lookup"><span data-stu-id="92388-1851">Application Pools</span></span>

<span data-ttu-id="92388-1852">Quando si ospitano più siti Web in un server, è consigliabile isolare le app le une dalle altre eseguendo ogni app nel relativo pool di applicazioni.</span><span class="sxs-lookup"><span data-stu-id="92388-1852">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="92388-1853">La finestra di dialogo **Aggiungi sito Web** di IIS ha questa configurazione come impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1853">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="92388-1854">Quando si specifica un valore in **Nome del sito**, il testo viene automaticamente trasferito alla casella di testo **Pool di applicazioni**.</span><span class="sxs-lookup"><span data-stu-id="92388-1854">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="92388-1855">Quando si aggiunge il sito viene creato un nuovo pool di applicazioni con il nome del sito.</span><span class="sxs-lookup"><span data-stu-id="92388-1855">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-identity"></a><span data-ttu-id="92388-1856">Pool di applicazioniIdentity</span><span class="sxs-lookup"><span data-stu-id="92388-1856">Application Pool Identity</span></span>

<span data-ttu-id="92388-1857">Un account di identità del pool di app consente di eseguire un'app in un account univoco, senza dover creare e gestire domini o account locali.</span><span class="sxs-lookup"><span data-stu-id="92388-1857">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="92388-1858">In IIS 8.0 o versioni successive il processo di lavoro amministrazione IIS (WAS) crea un account virtuale con il nome del nuovo pool di applicazioni ed esegue i processi di lavoro del pool di applicazioni inclusi nell'account per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1858">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="92388-1859">Nella console di gestione IIS in **Impostazioni avanzate** per il pool di applicazioni, verificare che **Identity** sia impostato per utilizzare **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="92388-1859">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Finestra di dialogo Impostazioni avanzate del pool di applicazione](index/_static/apppool-identity.png)

<span data-ttu-id="92388-1861">Il processo di gestione IIS crea un identificatore sicuro con il nome del pool di app nel sistema di sicurezza di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-1861">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="92388-1862">Le risorse possono essere protette mediante questa identità,</span><span class="sxs-lookup"><span data-stu-id="92388-1862">Resources can be secured using this identity.</span></span> <span data-ttu-id="92388-1863">che tuttavia non è un account utente reale e non viene visualizzata nella console di gestione utenti di Windows.</span><span class="sxs-lookup"><span data-stu-id="92388-1863">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="92388-1864">Se il processo di lavoro IIS richiede l'accesso con privilegi elevati all'app, modificare l'elenco di controllo di accesso (ACL) della directory contenente l'app:</span><span class="sxs-lookup"><span data-stu-id="92388-1864">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="92388-1865">Aprire Esplora risorse, quindi spostarsi nella directory.</span><span class="sxs-lookup"><span data-stu-id="92388-1865">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="92388-1866">Fare clic con il pulsante destro del mouse sulla directory e scegliere **Proprietà**.</span><span class="sxs-lookup"><span data-stu-id="92388-1866">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="92388-1867">Nella scheda **Sicurezza** selezionare il pulsante **Modifica** e quindi il pulsante **Aggiungi**.</span><span class="sxs-lookup"><span data-stu-id="92388-1867">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="92388-1868">Fare clic sul pulsante **Percorsi** e verificare che il sistema sia selezionato.</span><span class="sxs-lookup"><span data-stu-id="92388-1868">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="92388-1869">Immettere **IIS AppPool\\<nome_pool_applicazioni>** nell'area **Immettere i nomi degli oggetti da selezionare**.</span><span class="sxs-lookup"><span data-stu-id="92388-1869">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="92388-1870">Fare clic sul pulsante **Controlla nomi**.</span><span class="sxs-lookup"><span data-stu-id="92388-1870">Select the **Check Names** button.</span></span> <span data-ttu-id="92388-1871">Per *DefaultAppPool* controllare i nomi usando **IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-1871">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="92388-1872">Quando il pulsante **Controlla nomi** è selezionato, nell'area dei nomi degli oggetti viene indicato un valore di **DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="92388-1872">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="92388-1873">Non è possibile immettere il nome del pool di applicazioni direttamente nell'area dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-1873">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="92388-1874">Usare il formato **IIS AppPool\\<nome_pool_applicazioni>** durante il controllo dei nomi degli oggetti.</span><span class="sxs-lookup"><span data-stu-id="92388-1874">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: il nome del pool di applicazioni "DefaultAppPool" viene aggiunto in coda a "IIS AppPool\" nell'area dei nomi degli oggetti prima di selezionare "Controlla nomi".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="92388-1876">Seleziona **OK**.</span><span class="sxs-lookup"><span data-stu-id="92388-1876">Select **OK**.</span></span>

   ![Finestra di dialogo Seleziona utenti o gruppi per la cartella dell'app: dopo aver selezionato "Controlla nomi", il nome dell'oggetto "DefaultAppPool" viene visualizzato nell'area dei nomi degli oggetti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="92388-1878">Le autorizzazioni di lettura ed esecuzione devono essere concesse per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1878">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="92388-1879">Fornire autorizzazioni aggiuntive in base alle necessità.</span><span class="sxs-lookup"><span data-stu-id="92388-1879">Provide additional permissions as needed.</span></span>

<span data-ttu-id="92388-1880">L'accesso può essere concesso anche dal prompt dei comandi usando lo strumento **ICACLS**.</span><span class="sxs-lookup"><span data-stu-id="92388-1880">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="92388-1881">Usando *DefaultAppPool* come esempio, viene eseguito il comando seguente:</span><span class="sxs-lookup"><span data-stu-id="92388-1881">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="92388-1882">Per altre informazioni, vedere l'argomento [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="92388-1882">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="92388-1883">Supporto HTTP/2</span><span class="sxs-lookup"><span data-stu-id="92388-1883">HTTP/2 support</span></span>

<span data-ttu-id="92388-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) è supportato per le distribuzioni out-of-process che soddisfano i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="92388-1884">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="92388-1885">Windows Server 2016/Windows 10 o versioni successive; IIS 10 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="92388-1885">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="92388-1886">Le connessioni server perimetrali pubbliche usano HTTP/2, mentre la connessione con proxy inverso al [server Kestrel](xref:fundamentals/servers/kestrel) usa HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-1886">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="92388-1887">Frame di destinazione: non applicabile alle distribuzioni out-of-process, in quanto la connessione HTTP/2 è gestita interamente da IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1887">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="92388-1888">Connessione TLS 1.2 o successiva</span><span class="sxs-lookup"><span data-stu-id="92388-1888">TLS 1.2 or later connection</span></span>

<span data-ttu-id="92388-1889">Se viene stabilita una connessione HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) corrisponde a `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="92388-1889">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="92388-1890">HTTP/2 è abilitato per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="92388-1890">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="92388-1891">Se non viene stabilita una connessione HTTP/2, la connessione esegue il fallback a HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="92388-1891">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="92388-1892">Per altre informazioni sulla configurazione di HTTP/2 con le distribuzioni IIS, vedere [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS).</span><span class="sxs-lookup"><span data-stu-id="92388-1892">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="92388-1893">Richieste preliminari CORS</span><span class="sxs-lookup"><span data-stu-id="92388-1893">CORS preflight requests</span></span>

<span data-ttu-id="92388-1894">*Questa sezione si applica solo alle app ASP.NET Core destinate a .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="92388-1894">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="92388-1895">Per un'app ASP.NET Core destinata a .NET Framework, le richieste OPTIONS non vengono passate all'app per impostazione predefinita in IIS.</span><span class="sxs-lookup"><span data-stu-id="92388-1895">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="92388-1896">Per informazioni su come configurare i gestori IIS dell'app in *Web. config* per passare le richieste di opzioni, vedere [abilitare le richieste tra le origini in API Web ASP.NET 2:](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)funzionamento di CORS.</span><span class="sxs-lookup"><span data-stu-id="92388-1896">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="92388-1897">Risorse di distribuzione per amministratori IIS</span><span class="sxs-lookup"><span data-stu-id="92388-1897">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="92388-1898">Documentazione di ISS</span><span class="sxs-lookup"><span data-stu-id="92388-1898">IIS documentation</span></span>](/iis)
* <span data-ttu-id="92388-1899">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8) (Introduzione a Gestione IIS in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-1899">[Getting Started with the IIS Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)</span></span>
* [<span data-ttu-id="92388-1900">Distribuzione di applicazioni .NET Core</span><span class="sxs-lookup"><span data-stu-id="92388-1900">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="92388-1901">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="92388-1901">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="92388-1902">Il sito IIS ufficiale di Microsoft</span><span class="sxs-lookup"><span data-stu-id="92388-1902">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="92388-1903">Libreria di contenuti tecnici di Windows Server</span><span class="sxs-lookup"><span data-stu-id="92388-1903">Windows Server technical content library</span></span>](/windows-server/windows-server)
* <span data-ttu-id="92388-1904">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis) (HTTP/2 in IIS)</span><span class="sxs-lookup"><span data-stu-id="92388-1904">[HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)</span></span>
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
