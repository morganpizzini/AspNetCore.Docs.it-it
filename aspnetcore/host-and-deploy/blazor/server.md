---
<span data-ttu-id="8014b-101">title: "host and deploy ASP.NET Core Blazor Server" Author: guardrex Description: "informazioni su come ospitare e distribuire un' Blazor app Server usando ASP.NET Core".</span><span class="sxs-lookup"><span data-stu-id="8014b-101">title: 'Host and deploy ASP.NET Core Blazor Server' author: guardrex description: 'Learn how to host and deploy a Blazor Server app using ASP.NET Core.'</span></span>
<span data-ttu-id="8014b-102">monikerRange:' >= aspnetcore-3,1' ms. Author: Riande ms. Custom: MVC ms. Date: 06/04/2020 no-loc:</span><span class="sxs-lookup"><span data-stu-id="8014b-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 06/04/2020 no-loc:</span></span>
- <span data-ttu-id="8014b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8014b-103">'Blazor'</span></span>
- <span data-ttu-id="8014b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8014b-104">'Identity'</span></span>
- <span data-ttu-id="8014b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8014b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="8014b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8014b-106">'Razor'</span></span>
- <span data-ttu-id="8014b-107">' SignalR ' UID: host-and-deploy/Blazer/server</span><span class="sxs-lookup"><span data-stu-id="8014b-107">'SignalR' uid: host-and-deploy/blazor/server</span></span>

---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="8014b-108">Ospitare e distribuire il Blazor Server</span><span class="sxs-lookup"><span data-stu-id="8014b-108">Host and deploy Blazor Server</span></span>

<span data-ttu-id="8014b-109">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8014b-109">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8014b-110">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="8014b-110">Host configuration values</span></span>

<span data-ttu-id="8014b-111">Le [ Blazor app Server](xref:blazor/hosting-models#blazor-server) possono accettare [valori di configurazione host generici](xref:fundamentals/host/generic-host#host-configuration).</span><span class="sxs-lookup"><span data-stu-id="8014b-111">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="8014b-112">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="8014b-112">Deployment</span></span>

<span data-ttu-id="8014b-113">L'uso del [ Blazor modello di hosting del server](xref:blazor/hosting-models#blazor-server) Blazor viene eseguito sul server dall'interno di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8014b-113">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8014b-114">Gli aggiornamenti dell'interfaccia utente, la gestione degli eventi e le chiamate JavaScript vengono gestiti tramite una [SignalR](xref:signalr/introduction) connessione.</span><span class="sxs-lookup"><span data-stu-id="8014b-114">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8014b-115">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8014b-115">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8014b-116">Visual Studio include il modello di progetto di \*\* Blazor app Server\*\* ( `blazorserverside` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="8014b-116">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="8014b-117">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="8014b-117">Scalability</span></span>

<span data-ttu-id="8014b-118">Pianificare una distribuzione per sfruttare al meglio l'infrastruttura disponibile per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="8014b-118">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="8014b-119">Vedere le risorse seguenti per gestire la Blazor scalabilità delle app Server:</span><span class="sxs-lookup"><span data-stu-id="8014b-119">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="8014b-120">[Nozioni fondamentali sulle Blazor app Server](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="8014b-120">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="8014b-121">Server di distribuzione</span><span class="sxs-lookup"><span data-stu-id="8014b-121">Deployment server</span></span>

<span data-ttu-id="8014b-122">Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che verrà esaurita dall'app quando le richieste degli utenti aumentano.</span><span class="sxs-lookup"><span data-stu-id="8014b-122">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="8014b-123">La memoria disponibile sul server influiscono su:</span><span class="sxs-lookup"><span data-stu-id="8014b-123">The available memory on the server affects the:</span></span>

* <span data-ttu-id="8014b-124">Numero di circuiti attivi che un server è in grado di supportare.</span><span class="sxs-lookup"><span data-stu-id="8014b-124">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="8014b-125">Latenza dell'interfaccia utente nel client.</span><span class="sxs-lookup"><span data-stu-id="8014b-125">UI latency on the client.</span></span>

<span data-ttu-id="8014b-126">Per istruzioni sulla creazione di app Server sicure e scalabili Blazor , vedere <xref:security/blazor/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="8014b-126">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="8014b-127">Ogni circuito utilizza circa 250 KB di memoria per un'app di tipo *Hello World*minima.</span><span class="sxs-lookup"><span data-stu-id="8014b-127">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="8014b-128">La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente.</span><span class="sxs-lookup"><span data-stu-id="8014b-128">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="8014b-129">Si consiglia di misurare le richieste di risorse durante lo sviluppo dell'applicazione e dell'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione della destinazione di distribuzione: se si prevede che l'app supporti 5.000 utenti simultanei, valutare la possibilità di prevedere almeno 1,3 GB di memoria del server per l'app (o ~ 273 KB per utente).</span><span class="sxs-lookup"><span data-stu-id="8014b-129">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="8014b-130">configurazione</span><span class="sxs-lookup"><span data-stu-id="8014b-130"> configuration</span></span>

Blazor<span data-ttu-id="8014b-131">Le app server usano ASP.NET Core SignalR per comunicare con il browser.</span><span class="sxs-lookup"><span data-stu-id="8014b-131"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="8014b-132">[ SignalR le condizioni di hosting e scalabilità sono](xref:signalr/publish-to-azure-web-app) valide per Blazor App Server.</span><span class="sxs-lookup"><span data-stu-id="8014b-132">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="8014b-133">funziona meglio quando si usano WebSocket come il SignalR trasporto a causa di una latenza, affidabilità e [sicurezza](xref:signalr/security)più bassa.</span><span class="sxs-lookup"><span data-stu-id="8014b-133"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="8014b-134">Il polling prolungato viene usato da SignalR quando i WebSocket non sono disponibili o quando l'app è configurata in modo esplicito per l'uso del polling prolungato.</span><span class="sxs-lookup"><span data-stu-id="8014b-134">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="8014b-135">Quando si esegue la distribuzione nel servizio app Azure, configurare l'app per l'uso di WebSocket nelle impostazioni portale di Azure per il servizio.</span><span class="sxs-lookup"><span data-stu-id="8014b-135">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="8014b-136">Per informazioni dettagliate sulla configurazione dell'app per il servizio app Azure, vedere le [ SignalR linee guida](xref:signalr/publish-to-azure-web-app)per la pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="8014b-136">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="8014b-137">Servizio di Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="8014b-137">Azure SignalR Service</span></span>

<span data-ttu-id="8014b-138">È consigliabile usare il [ SignalR servizio di Azure](/azure/azure-signalr) per le Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="8014b-138">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="8014b-139">Il servizio consente di ridimensionare un' Blazor app Server a un numero elevato di connessioni simultanee SignalR .</span><span class="sxs-lookup"><span data-stu-id="8014b-139">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="8014b-140">Inoltre, la SignalR portata globale del servizio e i Data Center a prestazioni elevate facilitano significativamente la riduzione della latenza dovuta all'area geografica.</span><span class="sxs-lookup"><span data-stu-id="8014b-140">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="8014b-141">Per configurare un'app e, facoltativamente, effettuare il provisioning del servizio di Azure SignalR :</span><span class="sxs-lookup"><span data-stu-id="8014b-141">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="8014b-142">Consentire al servizio di supportare le *sessioni permanenti*, in cui i client vengono [reindirizzati allo stesso server durante il prerendering](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="8014b-142">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="8014b-143">Impostare l' `ServerStickyMode` opzione o il valore di configurazione su `Required` .</span><span class="sxs-lookup"><span data-stu-id="8014b-143">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="8014b-144">In genere, un'app crea la configurazione usando **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="8014b-144">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="8014b-145">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8014b-145">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="8014b-146">Configurazione (usare **uno** degli approcci seguenti):</span><span class="sxs-lookup"><span data-stu-id="8014b-146">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="8014b-147">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8014b-147">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="8014b-148">Impostazioni dell'applicazione di **configurazione**del servizio app  >  **Application settings** nel portale di Azure (**nome**: `Azure:SignalR:ServerStickyMode` , **valore**: `Required` ).</span><span class="sxs-lookup"><span data-stu-id="8014b-148">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="8014b-149">Creare un profilo di pubblicazione per le app di Azure in Visual Studio per l' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="8014b-149">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="8014b-150">Aggiungere la dipendenza del \*\* SignalR servizio di Azure\*\* al profilo.</span><span class="sxs-lookup"><span data-stu-id="8014b-150">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="8014b-151">Se la sottoscrizione di Azure non dispone di un'istanza del servizio di Azure preesistente SignalR da assegnare all'app, selezionare **Crea una nuova SignalR istanza del servizio di Azure** per eseguire il provisioning di una nuova istanza del servizio.</span><span class="sxs-lookup"><span data-stu-id="8014b-151">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="8014b-152">Pubblicare l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="8014b-152">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="8014b-153">IIS</span><span class="sxs-lookup"><span data-stu-id="8014b-153">IIS</span></span>

<span data-ttu-id="8014b-154">Quando si usa IIS, abilitare:</span><span class="sxs-lookup"><span data-stu-id="8014b-154">When using IIS, enable:</span></span>

* <span data-ttu-id="8014b-155">[WebSocket in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="8014b-155">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="8014b-156">[Sessioni permanenti con routing della richiesta dell'applicazione](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="8014b-156">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="8014b-157">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="8014b-157">Kubernetes</span></span>

<span data-ttu-id="8014b-158">Creare una definizione di ingresso con le [annotazioni Kubernetes seguenti per le sessioni permanenti](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="8014b-158">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="8014b-159">Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="8014b-159">Linux with Nginx</span></span>

<span data-ttu-id="8014b-160">Per SignalR il corretto funzionamento di WebSocket, verificare che le `Upgrade` intestazioni e del proxy `Connection` siano impostate sui valori seguenti e che `$connection_upgrade` sia mappato a uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="8014b-160">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="8014b-161">Valore dell'intestazione di aggiornamento per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="8014b-161">The Upgrade header value by default.</span></span>
* <span data-ttu-id="8014b-162">`close`Quando l'intestazione di aggiornamento è mancante o vuota.</span><span class="sxs-lookup"><span data-stu-id="8014b-162">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="8014b-163">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="8014b-163">For more information, see the following articles:</span></span>

* [<span data-ttu-id="8014b-164">NGINX come proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="8014b-164">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="8014b-165">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="8014b-165">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="8014b-166">Linux con Apache</span><span class="sxs-lookup"><span data-stu-id="8014b-166">Linux with Apache</span></span>

<span data-ttu-id="8014b-167">Per ospitare un' Blazor app dietro Apache in Linux, configurare `ProxyPass` per il traffico HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="8014b-167">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="8014b-168">Nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8014b-168">In the following example:</span></span>

* <span data-ttu-id="8014b-169">Il server gheppio è in esecuzione nel computer host.</span><span class="sxs-lookup"><span data-stu-id="8014b-169">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="8014b-170">L'app è in ascolto del traffico sulla porta 5000.</span><span class="sxs-lookup"><span data-stu-id="8014b-170">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="8014b-171">Abilitare i moduli seguenti:</span><span class="sxs-lookup"><span data-stu-id="8014b-171">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="8014b-172">Controllare gli errori di WebSocket nella console del browser.</span><span class="sxs-lookup"><span data-stu-id="8014b-172">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="8014b-173">Errori di esempio:</span><span class="sxs-lookup"><span data-stu-id="8014b-173">Example errors:</span></span>

* <span data-ttu-id="8014b-174">Firefox non è in grado di stabilire una connessione al server in ws://the-domain-name.tld/_blazor?id=XXX.</span><span class="sxs-lookup"><span data-stu-id="8014b-174">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="8014b-175">Errore: non è stato possibile avviare il trasporto ' WebSocket ': errore: si è verificato un errore con il trasporto.</span><span class="sxs-lookup"><span data-stu-id="8014b-175">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="8014b-176">Errore: non è stato possibile avviare il trasporto ' LongPolling ': TypeError: il trasporto non è definito</span><span class="sxs-lookup"><span data-stu-id="8014b-176">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="8014b-177">Errore: Impossibile connettersi al server con i trasporti disponibili.</span><span class="sxs-lookup"><span data-stu-id="8014b-177">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="8014b-178">WebSocket non riusciti</span><span class="sxs-lookup"><span data-stu-id="8014b-178">WebSockets failed</span></span>
* <span data-ttu-id="8014b-179">Errore: Impossibile inviare dati se la connessione non è nello stato "connesso".</span><span class="sxs-lookup"><span data-stu-id="8014b-179">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="8014b-180">Per ulteriori informazioni, vedere la [documentazione di Apache](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="8014b-180">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="8014b-181">Misurare la latenza di rete</span><span class="sxs-lookup"><span data-stu-id="8014b-181">Measure network latency</span></span>

<span data-ttu-id="8014b-182">L' [interoperabilità JS](xref:blazor/call-javascript-from-dotnet) può essere usata per misurare la latenza di rete, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="8014b-182">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code
{
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnInitializedAsync()
    {
        startTime = DateTime.UtcNow;
        var _ = await JS.InvokeAsync<string>("toString");
        latency = DateTime.UtcNow - startTime;
    }
}
```

<span data-ttu-id="8014b-183">Per un'esperienza dell'interfaccia utente ragionevole, è consigliabile una latenza di interfaccia utente prolungata di 250ms o meno.</span><span class="sxs-lookup"><span data-stu-id="8014b-183">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
