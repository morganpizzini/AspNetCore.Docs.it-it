---
title: Ospitare e distribuire ASP.NET Core Blazor server
author: guardrex
description: Informazioni su come ospitare e distribuire un' Blazor app Server usando ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 380bbab8898b4fbeab4efa514b17b807accbb1ac
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205865"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="99b89-103">Ospitare e distribuire Blazor il server</span><span class="sxs-lookup"><span data-stu-id="99b89-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="99b89-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="99b89-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="99b89-105">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="99b89-105">Host configuration values</span></span>

<span data-ttu-id="99b89-106">Le app Server possono accettare [valori di configurazione host generici](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="99b89-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="99b89-107">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="99b89-107">Deployment</span></span>

<span data-ttu-id="99b89-108">L'uso del modello di hosting Blazor del [ Blazor server](xref:blazor/hosting-models#blazor-server)viene eseguito sul server dall'interno di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99b89-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="99b89-109">Gli aggiornamenti dell'interfaccia utente, la gestione degli eventi e le chiamate [SignalR](xref:signalr/introduction) JavaScript vengono gestiti tramite una connessione.</span><span class="sxs-lookup"><span data-stu-id="99b89-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="99b89-110">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99b89-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="99b89-111">Visual Studio include il modello di progetto di \*\* Blazor app Server\*\* (`blazorserverside` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ).</span><span class="sxs-lookup"><span data-stu-id="99b89-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="99b89-112">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="99b89-112">Scalability</span></span>

<span data-ttu-id="99b89-113">Pianificare una distribuzione per sfruttare al meglio l'infrastruttura disponibile per un' Blazor app Server.</span><span class="sxs-lookup"><span data-stu-id="99b89-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="99b89-114">Vedere le risorse seguenti per gestire Blazor la scalabilità delle app Server:</span><span class="sxs-lookup"><span data-stu-id="99b89-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="99b89-115">[Nozioni fondamentali Blazor sulle app Server](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="99b89-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="99b89-116">Server di distribuzione</span><span class="sxs-lookup"><span data-stu-id="99b89-116">Deployment server</span></span>

<span data-ttu-id="99b89-117">Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che verrà esaurita dall'app quando le richieste degli utenti aumentano.</span><span class="sxs-lookup"><span data-stu-id="99b89-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="99b89-118">La memoria disponibile sul server influiscono su:</span><span class="sxs-lookup"><span data-stu-id="99b89-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="99b89-119">Numero di circuiti attivi che un server è in grado di supportare.</span><span class="sxs-lookup"><span data-stu-id="99b89-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="99b89-120">Latenza dell'interfaccia utente nel client.</span><span class="sxs-lookup"><span data-stu-id="99b89-120">UI latency on the client.</span></span>

<span data-ttu-id="99b89-121">Per istruzioni sulla creazione di app Server Blazor sicure e scalabili <xref:security/blazor/server/threat-mitigation>, vedere.</span><span class="sxs-lookup"><span data-stu-id="99b89-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server/threat-mitigation>.</span></span>

<span data-ttu-id="99b89-122">Ogni circuito utilizza circa 250 KB di memoria per un'app di tipo *Hello World*minima.</span><span class="sxs-lookup"><span data-stu-id="99b89-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="99b89-123">La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente.</span><span class="sxs-lookup"><span data-stu-id="99b89-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="99b89-124">Si consiglia di misurare le richieste di risorse durante lo sviluppo dell'applicazione e dell'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione della destinazione di distribuzione: se si prevede che l'app supporti 5.000 utenti simultanei, valutare la possibilità di prevedere almeno 1,3 GB di memoria del server per l'app (o ~ 273 KB per utente).</span><span class="sxs-lookup"><span data-stu-id="99b89-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a>SignalR<span data-ttu-id="99b89-125">configurazione</span><span class="sxs-lookup"><span data-stu-id="99b89-125"> configuration</span></span>

Blazor<span data-ttu-id="99b89-126">Le app server usano SignalR ASP.NET Core per comunicare con il browser.</span><span class="sxs-lookup"><span data-stu-id="99b89-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="99b89-127">[le condizioni di hosting e scalabilità sono valide per le app Server. SignalR](xref:signalr/publish-to-azure-web-app) Blazor</span><span class="sxs-lookup"><span data-stu-id="99b89-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="99b89-128">funziona meglio quando si usano WebSocket come il trasporto SignalR a causa di una latenza, affidabilità e [sicurezza](xref:signalr/security)più bassa.</span><span class="sxs-lookup"><span data-stu-id="99b89-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="99b89-129">Il polling prolungato SignalR viene usato da quando i WebSocket non sono disponibili o quando l'app è configurata in modo esplicito per l'uso del polling prolungato.</span><span class="sxs-lookup"><span data-stu-id="99b89-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="99b89-130">Quando si esegue la distribuzione nel servizio app Azure, configurare l'app per l'uso di WebSocket nelle impostazioni portale di Azure per il servizio.</span><span class="sxs-lookup"><span data-stu-id="99b89-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="99b89-131">Per informazioni dettagliate sulla configurazione dell'app per il servizio app Azure, vedere le [ SignalR linee guida](xref:signalr/publish-to-azure-web-app)per la pubblicazione.</span><span class="sxs-lookup"><span data-stu-id="99b89-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="99b89-132">Servizio SignalR di Azure</span><span class="sxs-lookup"><span data-stu-id="99b89-132">Azure SignalR Service</span></span>

<span data-ttu-id="99b89-133">È consigliabile usare il [servizio SignalR ](/azure/azure-signalr) di Azure Blazor per le app Server.</span><span class="sxs-lookup"><span data-stu-id="99b89-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="99b89-134">Il servizio consente di ridimensionare un' Blazor app Server a un numero elevato di connessioni SignalR simultanee.</span><span class="sxs-lookup"><span data-stu-id="99b89-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="99b89-135">Inoltre, la portata SignalR globale del servizio e i Data Center a prestazioni elevate facilitano significativamente la riduzione della latenza dovuta all'area geografica.</span><span class="sxs-lookup"><span data-stu-id="99b89-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="99b89-136">Per configurare un'app e, facoltativamente, effettuare il provisioning del servizio di Azure SignalR :</span><span class="sxs-lookup"><span data-stu-id="99b89-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="99b89-137">Consentire al servizio di supportare le *sessioni permanenti*, in cui i client vengono [reindirizzati allo stesso server durante il prerendering](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="99b89-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="99b89-138">Impostare l' `ServerStickyMode` opzione o il valore di `Required`configurazione su.</span><span class="sxs-lookup"><span data-stu-id="99b89-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="99b89-139">In genere, un'app crea la configurazione usando **uno** degli approcci seguenti:</span><span class="sxs-lookup"><span data-stu-id="99b89-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="99b89-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="99b89-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="99b89-141">Configurazione (usare **uno** degli approcci seguenti):</span><span class="sxs-lookup"><span data-stu-id="99b89-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="99b89-142">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="99b89-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="99b89-143">**Impostazioni dell'applicazione** di **configurazione** > del servizio app nel portale di Azure (**nome**: `Azure:SignalR:ServerStickyMode`, **valore**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="99b89-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="99b89-144">Creare un profilo di pubblicazione per le app di Azure in Blazor Visual Studio per l'app Server.</span><span class="sxs-lookup"><span data-stu-id="99b89-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="99b89-145">Aggiungere la dipendenza del \*\*servizio di Azure SignalR \*\* al profilo.</span><span class="sxs-lookup"><span data-stu-id="99b89-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="99b89-146">Se la sottoscrizione di Azure non dispone di un'istanza del SignalR servizio di Azure preesistente da assegnare all'app, selezionare **Crea SignalR una nuova istanza del servizio di Azure** per eseguire il provisioning di una nuova istanza del servizio.</span><span class="sxs-lookup"><span data-stu-id="99b89-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="99b89-147">Pubblicare l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="99b89-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="99b89-148">IIS</span><span class="sxs-lookup"><span data-stu-id="99b89-148">IIS</span></span>

<span data-ttu-id="99b89-149">Quando si usa IIS, abilitare:</span><span class="sxs-lookup"><span data-stu-id="99b89-149">When using IIS, enable:</span></span>

* <span data-ttu-id="99b89-150">[WebSocket in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="99b89-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="99b89-151">[Sessioni permanenti con routing della richiesta dell'applicazione](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="99b89-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="99b89-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="99b89-152">Kubernetes</span></span>

<span data-ttu-id="99b89-153">Creare una definizione di ingresso con le [annotazioni Kubernetes seguenti per le sessioni permanenti](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span><span class="sxs-lookup"><span data-stu-id="99b89-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="99b89-154">Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="99b89-154">Linux with Nginx</span></span>

<span data-ttu-id="99b89-155">Per SignalR il corretto funzionamento di WebSocket, verificare che le intestazioni `Upgrade` e `Connection` del proxy siano impostate sui valori seguenti e che `$connection_upgrade` sia mappato a uno dei seguenti valori:</span><span class="sxs-lookup"><span data-stu-id="99b89-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="99b89-156">Valore dell'intestazione di aggiornamento per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="99b89-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="99b89-157">`close`Quando l'intestazione di aggiornamento è mancante o vuota.</span><span class="sxs-lookup"><span data-stu-id="99b89-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="99b89-158">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="99b89-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="99b89-159">NGINX come proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="99b89-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="99b89-160">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="99b89-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="99b89-161">Misurare la latenza di rete</span><span class="sxs-lookup"><span data-stu-id="99b89-161">Measure network latency</span></span>

<span data-ttu-id="99b89-162">L' [interoperabilità JS](xref:blazor/call-javascript-from-dotnet) può essere usata per misurare la latenza di rete, come illustrato nell'esempio seguente:</span><span class="sxs-lookup"><span data-stu-id="99b89-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="99b89-163">Per un'esperienza dell'interfaccia utente ragionevole, è consigliabile una latenza di interfaccia utente prolungata di 250ms o meno.</span><span class="sxs-lookup"><span data-stu-id="99b89-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
