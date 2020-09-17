---
title: Differenze tra SignalR e ASP.NET Core SignalR
author: bradygaster
description: Differenze tra SignalR e ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: 965fbb3d8007cb64aaf51d82d87ed7a3a5298e9b
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721788"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>Differenze tra ASP.NET SignalR e ASP.NET Core SignalR

ASP.NET Core SignalR non è compatibile con i client o i server per ASP.NET SignalR . Questo articolo descrive in dettaglio le funzionalità che sono state rimosse o modificate in ASP.NET Core SignalR .

## <a name="how-to-identify-the-no-locsignalr-version"></a>Come identificare la SignalR versione

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pacchetto NuGet server** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | No. Incluso nel Framework condiviso [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| **Pacchetti NuGet client** | [Microsoft. AspNet. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft SignalR . AspNetCore. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pacchetto NPM client JavaScript** | [SignalR](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Client Java** | [Repository GitHub](https://github.com/SignalR/java-client) (deprecato)  | Pacchetto Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Tipo di app Server** | ASP.NET (System. Web) o OWIN Self-host | ASP.NET Core |
| **Piattaforme server supportate** | .NET Framework 4,5 o versione successiva | .NET Core 3,0 o versione successiva |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pacchetto NuGet server** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **Pacchetti NuGet client** | [Microsoft. AspNet. SignalR . Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft SignalR . AspNetCore. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pacchetto NPM client JavaScript** | [SignalR](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Client Java** | [Repository GitHub](https://github.com/SignalR/java-client) (deprecato)  | Pacchetto Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Tipo di app Server** | ASP.NET (System. Web) o OWIN Self-host | ASP.NET Core |
| **Piattaforme server supportate** | .NET Framework 4,5 o versione successiva | .NET Framework 4.6.1 o versioni successive<br>.NET Core 2,1 o versione successiva |

::: moniker-end

## <a name="feature-differences"></a>Differenze tra le funzionalità

### <a name="automatic-reconnects"></a>Riconnessioni automatiche

::: moniker range=">= aspnetcore-3.0"

In ASP.NET SignalR :

* Per impostazione predefinita, SignalR tenta di riconnettersi al server se la connessione viene eliminata. 

In ASP.NET Core SignalR :

* Le riconnessioni automatiche sono esplicite per il client [.NET](xref:signalr/dotnet-client#automatically-reconnect) e il [client JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Prima di ASP.NET Core 3,0, SignalR non supporta la riconnessione automatica. Se il client è disconnesso, l'utente deve avviare in modo esplicito una nuova connessione per riconnettersi. In ASP.NET SignalR SignalR tenta di riconnettersi al server se la connessione viene eliminata.

::: moniker-end

### <a name="protocol-support"></a>Supporto dei protocolli

ASP.NET Core SignalR supporta JSON, nonché un nuovo protocollo binario basato su [MessagePack](xref:signalr/messagepackhubprotocol). Inoltre, è possibile creare protocolli personalizzati.

### <a name="transports"></a>Trasporti

Il trasporto con frame Forever non è supportato in ASP.NET Core SignalR .

## <a name="differences-on-the-server"></a>Differenze sul server

Le SignalR librerie del lato server ASP.NET Core sono incluse in [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), che viene usato nel modello di **applicazione Web ASP.NET Core** per i Razor progetti MVC e.

ASP.NET Core SignalR è un middleware di ASP.NET Core. Deve essere configurata chiamando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Per configurare il routing, mappare le route agli hub all'interno della <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> chiamata al metodo nel `Startup.Configure` metodo.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Per configurare il routing, mappare le route agli hub all'interno della <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> chiamata al metodo nel `Startup.Configure` metodo.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sessioni permanenti

Il modello di scalabilità orizzontale per ASP.NET SignalR consente ai client di riconnettersi e inviare messaggi a qualsiasi server della farm. In ASP.NET Core SignalR , il client deve interagire con lo stesso server per la durata della connessione. Per la scalabilità orizzontale con Redis, significa che sono necessarie sessioni permanenti. Per la scalabilità orizzontale tramite il [ SignalR servizio Azure](/azure/azure-signalr/), le sessioni permanenti non sono necessarie perché il servizio gestisce le connessioni ai client.

### <a name="single-hub-per-connection"></a>Hub singolo per connessione

In ASP.NET Core SignalR , il modello di connessione è stato semplificato. Le connessioni vengono effettuate direttamente in un singolo hub, anziché una singola connessione usata per condividere l'accesso a più hub.

### <a name="streaming"></a>Streaming

ASP.NET Core SignalR supporta ora lo [streaming dei dati](xref:signalr/streaming) dall'hub al client.

### <a name="state"></a>State

La possibilità di passare uno stato arbitrario tra i client e l'hub (spesso chiamato `HubState` ) è stata rimossa, oltre al supporto per i messaggi di stato. Al momento non è presente alcuna controparte dei proxy dell'hub.

### <a name="persistentconnection-removal"></a>Rimozione di PersistentConnection

In ASP.NET Core SignalR , la classe [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) è stata rimossa.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core dispone DI INSERIMENTO DI dipendenze (DI) incorporato nel Framework. I servizi possono usare l'inserimento DI dipendenze per accedere a [HubContext](xref:signalr/hubcontext). L' `GlobalHost` oggetto usato in ASP.NET SignalR per ottenere un oggetto `HubContext` non esiste nel ASP.NET Core SignalR .

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR non dispone del supporto per i `HubPipeline` moduli.

## <a name="differences-on-the-client"></a>Differenze sul client

### <a name="typescript"></a>TypeScript

Il SignalR client ASP.NET Core è scritto in [typescript](https://www.typescriptlang.org/). È possibile scrivere in JavaScript o TypeScript quando si usa il [client JavaScript](xref:signalr/javascript-client).

### <a name="the-javascript-client-is-hosted-at-npm"></a>Il client JavaScript è ospitato in NPM

::: moniker range=">= aspnetcore-3.0"

Nelle versioni ASP.NET il client JavaScript è stato ottenuto tramite un pacchetto NuGet in Visual Studio. Nelle versioni ASP.NET Core il [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pacchetto NPM contiene le librerie JavaScript. Questo pacchetto non è incluso nel modello di **applicazione Web ASP.NET Core** . Usare NPM per ottenere e installare il `@microsoft/signalr` pacchetto NPM.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Nelle versioni ASP.NET il client JavaScript è stato ottenuto tramite un pacchetto NuGet in Visual Studio. Nelle versioni ASP.NET Core il [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pacchetto NPM contiene le librerie JavaScript. Questo pacchetto non è incluso nel modello di **applicazione Web ASP.NET Core** . Usare NPM per ottenere e installare il `@aspnet/signalr` pacchetto NPM.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

La dipendenza da jQuery è stata rimossa, tuttavia i progetti possono comunque usare jQuery.

### <a name="internet-explorer-support"></a>Supporto di Internet Explorer

ASP.NET Core SignalR richiede Microsoft Internet Explorer 11 o versione successiva (ASP.NET SignalR supporta Microsoft Internet Explorer 8 e versioni successive).

### <a name="javascript-client-method-syntax"></a>Sintassi del metodo client JavaScript

::: moniker range=">= aspnetcore-3.0"

La sintassi JavaScript è stata modificata rispetto alla versione ASP.NET di SignalR . Anziché usare l' `$connection` oggetto, creare una connessione usando l'API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Usare il metodo [on](/javascript/api/@microsoft/signalr/HubConnection#on) per specificare i metodi client che l'hub può chiamare.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

La sintassi JavaScript è stata modificata rispetto alla versione ASP.NET di SignalR . Anziché usare l' `$connection` oggetto, creare una connessione usando l'API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Usare il metodo [on](/javascript/api/@aspnet/signalr/HubConnection#on) per specificare i metodi client che l'hub può chiamare.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Dopo aver creato il metodo client, avviare la connessione dell'hub. Concatenare un metodo [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) per registrare o gestire gli errori.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Proxy Hub

::: moniker range=">= aspnetcore-3.0"

I proxy Hub non vengono più generati automaticamente. Al contrario, il nome del metodo viene passato nell'API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) sotto forma di stringa.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

I proxy Hub non vengono più generati automaticamente. Al contrario, il nome del metodo viene passato nell'API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) sotto forma di stringa.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET e altri client

[Microsoft. AspNetCore. SignalR ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Il pacchetto NuGet client contiene le librerie client .NET per ASP.NET Core SignalR .

Usare <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> per creare e compilare un'istanza di una connessione a un hub.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Differenze di scalabilità orizzontale

ASP.NET SignalR supporta SQL Server e Redis. ASP.NET Core SignalR supporta il SignalR servizio di Azure e Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalR scalabilità orizzontale con il bus di servizio di Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR scalabilità orizzontale con Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR scalabilità orizzontale con SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Servizio di Azure SignalR](/azure/azure-signalr/)
* [Backplane Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Hub](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
* [Client .NET](xref:signalr/dotnet-client)
* [Piattaforme supportate](xref:signalr/supported-platforms)