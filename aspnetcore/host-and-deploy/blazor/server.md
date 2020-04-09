---
title: Ospitare e distribuire Blazor ASP.NET Server di base
author: guardrex
description: Informazioni su come ospitare Blazor e distribuire un'app Server usando ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: 866bb348180c872d8ab20787283cfb7217183a8d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "79025418"
---
# <a name="host-and-deploy-opno-locblazor-server"></a><span data-ttu-id="b6b38-103">Ospitare e Blazor distribuire Server</span><span class="sxs-lookup"><span data-stu-id="b6b38-103">Host and deploy Blazor Server</span></span>

<span data-ttu-id="b6b38-104">Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b6b38-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b6b38-105">Valori di configurazione dell'host</span><span class="sxs-lookup"><span data-stu-id="b6b38-105">Host configuration values</span></span>

<span data-ttu-id="b6b38-106">Le app server possono accettare valori di [configurazione host generici.](xref:fundamentals/host/generic-host#host-configuration) [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="b6b38-106">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="b6b38-107">Distribuzione</span><span class="sxs-lookup"><span data-stu-id="b6b38-107">Deployment</span></span>

<span data-ttu-id="b6b38-108">Utilizzando [ Blazor ](xref:blazor/hosting-models#blazor-server)il modello Blazor di hosting Server , viene eseguito sul server dall'interno di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6b38-108">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b6b38-109">Gli aggiornamenti dell'interfaccia utente, la gestione [SignalR](xref:signalr/introduction) degli eventi e le chiamate JavaScript vengono gestiti tramite una connessione.</span><span class="sxs-lookup"><span data-stu-id="b6b38-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b6b38-110">È necessario un server Web in grado di ospitare un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6b38-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b6b38-111">Visual Studio \*\* Blazor \*\* include il modello`blazorserverside` di progetto App Server (modello quando si usa il [comando dotnet new).](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="b6b38-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="b6b38-112">Scalabilità</span><span class="sxs-lookup"><span data-stu-id="b6b38-112">Scalability</span></span>

<span data-ttu-id="b6b38-113">Pianificare una distribuzione per sfruttare al Blazor meglio l'infrastruttura disponibile per un'app Server.</span><span class="sxs-lookup"><span data-stu-id="b6b38-113">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="b6b38-114">Vedere le risorse Blazor seguenti per gestire la scalabilità delle app del server:See the following resources to address Server app scalability:</span><span class="sxs-lookup"><span data-stu-id="b6b38-114">See the following resources to address Blazor Server app scalability:</span></span>

* <span data-ttu-id="b6b38-115">[Nozioni Blazor fondamentali sulle app Server](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="b6b38-115">[Fundamentals of Blazor Server apps](xref:blazor/hosting-models#blazor-server)</span></span>
* <xref:security/blazor/server>

### <a name="deployment-server"></a><span data-ttu-id="b6b38-116">Server di distribuzione</span><span class="sxs-lookup"><span data-stu-id="b6b38-116">Deployment server</span></span>

<span data-ttu-id="b6b38-117">Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che l'app esaurirà man mano che aumentano le richieste degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b6b38-117">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="b6b38-118">La memoria disponibile sul server influisce su:</span><span class="sxs-lookup"><span data-stu-id="b6b38-118">The available memory on the server affects the:</span></span>

* <span data-ttu-id="b6b38-119">Numero di circuiti attivi che un server può supportare.</span><span class="sxs-lookup"><span data-stu-id="b6b38-119">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="b6b38-120">Latenza dell'interfaccia utente sul client.</span><span class="sxs-lookup"><span data-stu-id="b6b38-120">UI latency on the client.</span></span>

<span data-ttu-id="b6b38-121">Per indicazioni sulla creazione Blazor di app <xref:security/blazor/server>server sicure e scalabili, vedere .</span><span class="sxs-lookup"><span data-stu-id="b6b38-121">For guidance on building secure and scalable Blazor server apps, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="b6b38-122">Ogni circuito utilizza circa 250 KB di memoria per un'app minima in stile *Hello World.*</span><span class="sxs-lookup"><span data-stu-id="b6b38-122">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="b6b38-123">La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente.</span><span class="sxs-lookup"><span data-stu-id="b6b38-123">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="b6b38-124">È consigliabile misurare le richieste di risorse durante lo sviluppo per l'app e l'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione dell'obiettivo di distribuzione: se si prevede che l'app supporti 5.000 utenti simultanei, prendere in considerazione l'impostazione del budget di almeno 1,3 GB di memoria del server per l'app (o 273 KB per utente).</span><span class="sxs-lookup"><span data-stu-id="b6b38-124">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="opno-locsignalr-configuration"></a>SignalR<span data-ttu-id="b6b38-125">Conﬁgurazione</span><span class="sxs-lookup"><span data-stu-id="b6b38-125"> configuration</span></span>

Blazor<span data-ttu-id="b6b38-126">Le app server SignalR usano ASP.NET Core per comunicare con il browser.</span><span class="sxs-lookup"><span data-stu-id="b6b38-126"> Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="b6b38-127">Le condizioni di hosting Blazor e [ridimensionamento si applicano alle app SignalR](xref:signalr/publish-to-azure-web-app) server.</span><span class="sxs-lookup"><span data-stu-id="b6b38-127">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

Blazor<span data-ttu-id="b6b38-128">funziona meglio quando si utilizza SignalR WebSockets come trasporto a causa di latenza, affidabilità e [sicurezza](xref:signalr/security)inferiori.</span><span class="sxs-lookup"><span data-stu-id="b6b38-128"> works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="b6b38-129">Il polling lungo SignalR viene usato quando WebSockets non è disponibile o quando l'app è configurata in modo esplicito per usare il polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b6b38-129">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="b6b38-130">Quando si esegue la distribuzione nel servizio app di Azure, configurare l'app per l'uso di WebSockets nelle impostazioni del portale di Azure per il servizio.</span><span class="sxs-lookup"><span data-stu-id="b6b38-130">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="b6b38-131">Per informazioni dettagliate sulla configurazione dell'app per il servizio app di Azure, vedere le linee guida per la [ SignalR pubblicazione.](xref:signalr/publish-to-azure-web-app)</span><span class="sxs-lookup"><span data-stu-id="b6b38-131">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-opno-locsignalr-service"></a><span data-ttu-id="b6b38-132">Servizio SignalR di AzureAzure Service</span><span class="sxs-lookup"><span data-stu-id="b6b38-132">Azure SignalR Service</span></span>

<span data-ttu-id="b6b38-133">È consigliabile usare Blazor il servizio di [Azure SignalR ](/azure/azure-signalr) per le app del server.</span><span class="sxs-lookup"><span data-stu-id="b6b38-133">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="b6b38-134">Il servizio consente di Blazor aumentare la scalabilità SignalR di un'app Server a un numero elevato di connessioni simultanee.</span><span class="sxs-lookup"><span data-stu-id="b6b38-134">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="b6b38-135">Inoltre, la SignalR portata globale del servizio e i data center ad alte prestazioni aiutano in modo significativo a ridurre la latenza dovuta all'area geografica.</span><span class="sxs-lookup"><span data-stu-id="b6b38-135">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span> <span data-ttu-id="b6b38-136">Per configurare un'app ed eseguire SignalR facoltativamente il provisioning, il servizio di Azure:To configure an app (and optionally provision) the Azure Service:</span><span class="sxs-lookup"><span data-stu-id="b6b38-136">To configure an app (and optionally provision) the Azure SignalR Service:</span></span>

1. <span data-ttu-id="b6b38-137">Abilitare il servizio per supportare *le sessioni permanenti,* in cui i client vengono [reindirizzati allo stesso server durante il prerendering.](xref:blazor/hosting-models#connection-to-the-server)</span><span class="sxs-lookup"><span data-stu-id="b6b38-137">Enable the service to support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="b6b38-138">Impostare `ServerStickyMode` l'opzione `Required`o il valore di configurazione su .</span><span class="sxs-lookup"><span data-stu-id="b6b38-138">Set the `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="b6b38-139">In genere, un'app crea la configurazione usando **uno** degli approcci seguenti:Typically, an app creates the configuration using one of the following approaches:</span><span class="sxs-lookup"><span data-stu-id="b6b38-139">Typically, an app creates the configuration using **one** of the following approaches:</span></span>

   * <span data-ttu-id="b6b38-140">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b6b38-140">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="b6b38-141">Configurazione (utilizzare **uno** dei seguenti approcci):</span><span class="sxs-lookup"><span data-stu-id="b6b38-141">Configuration (use **one** of the following approaches):</span></span>
  
     * <span data-ttu-id="b6b38-142">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="b6b38-142">*appsettings.json*:</span></span>

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * <span data-ttu-id="b6b38-143">Impostazioni dell'applicazione di **configurazione** > **Application settings** del servizio app `Azure:SignalR:ServerStickyMode`nel portale di Azure (**Nome**: , **Valore**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="b6b38-143">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure:SignalR:ServerStickyMode`, **Value**: `Required`).</span></span>

1. <span data-ttu-id="b6b38-144">Creare un profilo di pubblicazione di Blazor App di Azure in Visual Studio per l'app Server.Create an Azure Apps publish profile in Visual Studio for the Server app.</span><span class="sxs-lookup"><span data-stu-id="b6b38-144">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="b6b38-145">Aggiungere la dipendenza \*\*Servizio di Azure SignalR \*\* al profilo.</span><span class="sxs-lookup"><span data-stu-id="b6b38-145">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="b6b38-146">Se la sottoscrizione di Azure non dispone SignalR di un'istanza del servizio di Azure preesistente da assegnare all'app, selezionare Crea una nuova istanza del \*\*servizio di Azure SignalR \*\* per eseguire il provisioning di una nuova istanza del servizio.</span><span class="sxs-lookup"><span data-stu-id="b6b38-146">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="b6b38-147">Pubblicare l'app in Azure.</span><span class="sxs-lookup"><span data-stu-id="b6b38-147">Publish the app to Azure.</span></span>

#### <a name="iis"></a><span data-ttu-id="b6b38-148">IIS</span><span class="sxs-lookup"><span data-stu-id="b6b38-148">IIS</span></span>

<span data-ttu-id="b6b38-149">Quando si utilizza IIS, abilitare:</span><span class="sxs-lookup"><span data-stu-id="b6b38-149">When using IIS, enable:</span></span>

* <span data-ttu-id="b6b38-150">[WebSockets in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span><span class="sxs-lookup"><span data-stu-id="b6b38-150">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="b6b38-151">[Sessioni permanenti con Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span><span class="sxs-lookup"><span data-stu-id="b6b38-151">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="b6b38-152">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b6b38-152">Kubernetes</span></span>

<span data-ttu-id="b6b38-153">Creare una definizione in ingresso con le seguenti [annotazioni Kubernetes per le sessioni permanenti:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)</span><span class="sxs-lookup"><span data-stu-id="b6b38-153">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

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

#### <a name="linux-with-nginx"></a><span data-ttu-id="b6b38-154">Linux con Nginx</span><span class="sxs-lookup"><span data-stu-id="b6b38-154">Linux with Nginx</span></span>

<span data-ttu-id="b6b38-155">Affinché SignalR WebSockets funzioni correttamente, verificare che le intestazioni `Upgrade` e `Connection` `$connection_upgrade` il proxy siano impostati sui seguenti valori e che sia mappato a:</span><span class="sxs-lookup"><span data-stu-id="b6b38-155">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="b6b38-156">Il valore dell'intestazione Upgrade per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b6b38-156">The Upgrade header value by default.</span></span>
* <span data-ttu-id="b6b38-157">`close`quando l'intestazione Upgrade è mancante o vuota.</span><span class="sxs-lookup"><span data-stu-id="b6b38-157">`close` when the Upgrade header is missing or empty.</span></span>

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

<span data-ttu-id="b6b38-158">Per altre informazioni, vedere gli articoli seguenti:</span><span class="sxs-lookup"><span data-stu-id="b6b38-158">For more information, see the following articles:</span></span>

* [<span data-ttu-id="b6b38-159">NGINX come proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="b6b38-159">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="b6b38-160">Proxy WebSocket</span><span class="sxs-lookup"><span data-stu-id="b6b38-160">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a><span data-ttu-id="b6b38-161">Misurare la latenza di rete</span><span class="sxs-lookup"><span data-stu-id="b6b38-161">Measure network latency</span></span>

<span data-ttu-id="b6b38-162">[L'interoperabilità JS](xref:blazor/call-javascript-from-dotnet) può essere utilizzata per misurare la latenza di rete, come illustrato nell'esempio seguente:JS interop can be used to measure network latency, as the following example demonstrates:</span><span class="sxs-lookup"><span data-stu-id="b6b38-162">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

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

<span data-ttu-id="b6b38-163">Per un'esperienza ragionevole dell'interfaccia utente, è consigliabile una latenza costante dell'interfaccia utente di 250 ms o meno.</span><span class="sxs-lookup"><span data-stu-id="b6b38-163">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
