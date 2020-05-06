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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/server
ms.openlocfilehash: e69b91035c65739dde724330e83793c0b8b5481a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775154"
---
# <a name="host-and-deploy-blazor-server"></a>Ospitare e distribuire Blazor il server

Di [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) e [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Valori di configurazione dell'host

Le app Server possono accettare [valori di configurazione host generici](xref:fundamentals/host/generic-host#host-configuration). [ Blazor ](xref:blazor/hosting-models#blazor-server)

## <a name="deployment"></a>Distribuzione

L'uso del modello di hosting Blazor del [ Blazor server](xref:blazor/hosting-models#blazor-server)viene eseguito sul server dall'interno di un'app ASP.NET Core. Gli aggiornamenti dell'interfaccia utente, la gestione degli eventi e le chiamate [SignalR](xref:signalr/introduction) JavaScript vengono gestiti tramite una connessione.

È necessario un server Web in grado di ospitare un'app ASP.NET Core. Visual Studio include il modello di progetto di ** Blazor app Server** (`blazorserverside` modello quando si usa il comando [DotNet New](/dotnet/core/tools/dotnet-new) ).

## <a name="scalability"></a>Scalabilità

Pianificare una distribuzione per sfruttare al meglio l'infrastruttura disponibile per un' Blazor app Server. Vedere le risorse seguenti per gestire Blazor la scalabilità delle app Server:

* [Nozioni fondamentali Blazor sulle app Server](xref:blazor/hosting-models#blazor-server)
* <xref:security/blazor/server/threat-mitigation>

### <a name="deployment-server"></a>Server di distribuzione

Quando si considera la scalabilità di un singolo server (scalabilità verticale), la memoria disponibile per un'app è probabilmente la prima risorsa che verrà esaurita dall'app quando le richieste degli utenti aumentano. La memoria disponibile sul server influiscono su:

* Numero di circuiti attivi che un server è in grado di supportare.
* Latenza dell'interfaccia utente nel client.

Per istruzioni sulla creazione di app Server Blazor sicure e scalabili <xref:security/blazor/server/threat-mitigation>, vedere.

Ogni circuito utilizza circa 250 KB di memoria per un'app di tipo *Hello World*minima. La dimensione di un circuito dipende dal codice dell'app e dai requisiti di manutenzione dello stato associati a ogni componente. Si consiglia di misurare le richieste di risorse durante lo sviluppo dell'applicazione e dell'infrastruttura, ma la linea di base seguente può essere un punto di partenza per la pianificazione della destinazione di distribuzione: se si prevede che l'app supporti 5.000 utenti simultanei, valutare la possibilità di prevedere almeno 1,3 GB di memoria del server per l'app (o ~ 273 KB per utente).

### <a name="signalr-configuration"></a>SignalRconfigurazione

BlazorLe app server usano SignalR ASP.NET Core per comunicare con il browser. [le condizioni di hosting e scalabilità sono valide per le app Server. SignalR](xref:signalr/publish-to-azure-web-app) Blazor

Blazorfunziona meglio quando si usano WebSocket come il trasporto SignalR a causa di una latenza, affidabilità e [sicurezza](xref:signalr/security)più bassa. Il polling prolungato SignalR viene usato da quando i WebSocket non sono disponibili o quando l'app è configurata in modo esplicito per l'uso del polling prolungato. Quando si esegue la distribuzione nel servizio app Azure, configurare l'app per l'uso di WebSocket nelle impostazioni portale di Azure per il servizio. Per informazioni dettagliate sulla configurazione dell'app per il servizio app Azure, vedere le [ SignalR linee guida](xref:signalr/publish-to-azure-web-app)per la pubblicazione.

#### <a name="azure-signalr-service"></a>Servizio SignalR di Azure

È consigliabile usare il [servizio SignalR ](/azure/azure-signalr) di Azure Blazor per le app Server. Il servizio consente di ridimensionare un' Blazor app Server a un numero elevato di connessioni SignalR simultanee. Inoltre, la portata SignalR globale del servizio e i Data Center a prestazioni elevate facilitano significativamente la riduzione della latenza dovuta all'area geografica. Per configurare un'app e, facoltativamente, effettuare il provisioning del servizio di Azure SignalR :

1. Consentire al servizio di supportare le *sessioni permanenti*, in cui i client vengono [reindirizzati allo stesso server durante il prerendering](xref:blazor/hosting-models#connection-to-the-server). Impostare l' `ServerStickyMode` opzione o il valore di `Required`configurazione su. In genere, un'app crea la configurazione usando **uno** degli approcci seguenti:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Configurazione (usare **uno** degli approcci seguenti):
  
     * *appSettings. JSON*:

       ```json
       "Azure:SignalR:ServerStickyMode": "Required"
       ```

     * **Impostazioni dell'applicazione** di **configurazione** > del servizio app nel portale di Azure (**nome**: `Azure:SignalR:ServerStickyMode`, **valore**: `Required`).

1. Creare un profilo di pubblicazione per le app di Azure in Blazor Visual Studio per l'app Server.
1. Aggiungere la dipendenza del **servizio di Azure SignalR ** al profilo. Se la sottoscrizione di Azure non dispone di un'istanza del SignalR servizio di Azure preesistente da assegnare all'app, selezionare **Crea SignalR una nuova istanza del servizio di Azure** per eseguire il provisioning di una nuova istanza del servizio.
1. Pubblicare l'app in Azure.

#### <a name="iis"></a>IIS

Quando si usa IIS, abilitare:

* [WebSocket in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).
* [Sessioni permanenti con routing della richiesta dell'applicazione](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

#### <a name="kubernetes"></a>Kubernetes

Creare una definizione di ingresso con le [annotazioni Kubernetes seguenti per le sessioni permanenti](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):

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

Per SignalR il corretto funzionamento di WebSocket, verificare che le intestazioni `Upgrade` e `Connection` del proxy siano impostate sui valori seguenti e che `$connection_upgrade` sia mappato a uno dei seguenti valori:

* Valore dell'intestazione di aggiornamento per impostazione predefinita.
* `close`Quando l'intestazione di aggiornamento è mancante o vuota.

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

L' [interoperabilità JS](xref:blazor/call-javascript-from-dotnet) può essere usata per misurare la latenza di rete, come illustrato nell'esempio seguente:

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

Per un'esperienza dell'interfaccia utente ragionevole, è consigliabile una latenza di interfaccia utente prolungata di 250ms o meno.
