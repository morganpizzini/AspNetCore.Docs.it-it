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
# <a name="host-and-deploy-opno-locblazor-server"></a>Ospitare e Blazor distribuire Server

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valori di configurazione dell'host

Le app server possono accettare valori di [configurazione host generici.](xref:fundamentals/host/generic-host#host-configuration) [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Distribuzione

Utilizzando [ Blazor ](xref:blazor/hosting-models#blazor-server)il modello Blazor di hosting Server , viene eseguito sul server dall'interno di un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia utente, la gestione [SignalR](xref:signalr/introduction) degli eventi e le chiamate JavaScript vengono gestiti tramite una connessione.

È necessario un server Web in grado di ospitare un'app ASP.NET Core. Visual Studio ** Blazor ** include il modello`blazorserverside` di progetto App Server (modello quando si usa il [comando dotnet new).](/dotnet/core/tools/dotnet-new)

## <a name="scalability"></a>Scalabilità

Pianificare una distribuzione per sfruttare al Blazor meglio l'infrastruttura disponibile per un'app Server. Vedere le risorse Blazor seguenti per gestire la scalabilità delle app del server:See the following resources to address Server app scalability:

* [Nozioni Blazor fondamentali sulle app Server](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server>

### <a name="deployment-server"></a>Server di distribuzione

Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che l'app esaurirà man mano che aumentano le richieste degli utenti. La memoria disponibile sul server influisce su:

* Numero di circuiti attivi che un server può supportare.
* Latenza dell'interfaccia utente sul client.

Per indicazioni sulla creazione Blazor di app <xref:security/blazor/server>server sicure e scalabili, vedere .

Ogni circuito utilizza circa 250 KB di memoria per un'app minima in stile *Hello World.* La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente. È consigliabile misurare le richieste di risorse durante lo sviluppo per l'app e l'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione dell'obiettivo di distribuzione: se si prevede che l'app supporti 5.000 utenti simultanei, prendere in considerazione l'impostazione del budget di almeno 1,3 GB di memoria del server per l'app (o 273 KB per utente).

### <a name="opno-locsignalr-configuration"></a>SignalRConﬁgurazione

BlazorLe app server SignalR usano ASP.NET Core per comunicare con il browser. Le condizioni di hosting Blazor e [ridimensionamento si applicano alle app SignalR](xref:signalr/publish-to-azure-web-app) server.

Blazorfunziona meglio quando si utilizza SignalR WebSockets come trasporto a causa di latenza, affidabilità e [sicurezza](xref:signalr/security)inferiori. Il polling lungo SignalR viene usato quando WebSockets non è disponibile o quando l'app è configurata in modo esplicito per usare il polling lungo. Quando si esegue la distribuzione nel servizio app di Azure, configurare l'app per l'uso di WebSockets nelle impostazioni del portale di Azure per il servizio. Per informazioni dettagliate sulla configurazione dell'app per il servizio app di Azure, vedere le linee guida per la [ SignalR pubblicazione.](xref:signalr/publish-to-azure-web-app)

#### <a name="azure-opno-locsignalr-service"></a>Servizio SignalR di AzureAzure Service

È consigliabile usare Blazor il servizio di [Azure SignalR ](/azure/azure-signalr) per le app del server. Il servizio consente di Blazor aumentare la scalabilità SignalR di un'app Server a un numero elevato di connessioni simultanee. Inoltre, la SignalR portata globale del servizio e i data center ad alte prestazioni aiutano in modo significativo a ridurre la latenza dovuta all'area geografica. Per configurare un'app ed eseguire SignalR facoltativamente il provisioning, il servizio di Azure:To configure an app (and optionally provision) the Azure Service:

1. Abilitare il servizio per supportare *le sessioni permanenti,* in cui i client vengono [reindirizzati allo stesso server durante il prerendering.](xref:blazor/hosting-models#connection-to-the-server) Impostare `ServerStickyMode` l'opzione `Required`o il valore di configurazione su . In genere, un'app crea la configurazione usando **uno** degli approcci seguenti:Typically, an app creates the configuration using one of the following approaches:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configurazione (utilizzare **uno** dei seguenti approcci):
  
     * *appsettings.json*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * Impostazioni dell'applicazione di **configurazione** > **Application settings** del servizio app `Azure:SignalR:ServerStickyMode`nel portale di Azure (**Nome**: , **Valore**: `Required`).

1. Creare un profilo di pubblicazione di Blazor App di Azure in Visual Studio per l'app Server.Create an Azure Apps publish profile in Visual Studio for the Server app.
1. Aggiungere la dipendenza **Servizio di Azure SignalR ** al profilo. Se la sottoscrizione di Azure non dispone SignalR di un'istanza del servizio di Azure preesistente da assegnare all'app, selezionare Crea una nuova istanza del **servizio di Azure SignalR ** per eseguire il provisioning di una nuova istanza del servizio.
1. Pubblicare l'app in Azure.

#### <a name="iis"></a>IIS

Quando si utilizza IIS, abilitare:

* [WebSockets in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessioni permanenti con Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Creare una definizione in ingresso con le seguenti [annotazioni Kubernetes per le sessioni permanenti:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)

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

#### <a name="linux-with-nginx"></a>Linux con Nginx

Affinché SignalR WebSockets funzioni correttamente, verificare che le intestazioni `Upgrade` e `Connection` `$connection_upgrade` il proxy siano impostati sui seguenti valori e che sia mappato a:

* Il valore dell'intestazione Upgrade per impostazione predefinita.
* `close`quando l'intestazione Upgrade è mancante o vuota.

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

Per altre informazioni, vedere gli articoli seguenti:

* [NGINX come proxy WebSocket](https://www.nginx.com/blog/websocket-nginx/)
* [Proxy WebSocket](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

### <a name="measure-network-latency"></a>Misurare la latenza di rete

[L'interoperabilità JS](xref:blazor/call-javascript-from-dotnet) può essere utilizzata per misurare la latenza di rete, come illustrato nell'esempio seguente:JS interop can be used to measure network latency, as the following example demonstrates:

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

Per un'esperienza ragionevole dell'interfaccia utente, è consigliabile una latenza costante dell'interfaccia utente di 250 ms o meno.
