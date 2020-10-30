---
title: Client di ASP.NET Core SignalR
author: bradygaster
description: Informazioni sulle funzionalità supportate dai vari client di ASP.NET Core SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
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
uid: signalr/client-features
ms.openlocfilehash: 1d1778f95c53179ca05eaced3d1913f208220df4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050979"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>Client di ASP.NET Core SignalR

## <a name="versioning-support-and-compatibility"></a>Controllo delle versioni, supporto e compatibilità

I SignalR client vengono forniti insieme ai componenti server e vengono sottolineati con la versione corrispondente. Qualsiasi client supportato può connettersi in modo sicuro a qualsiasi server supportato e gli eventuali problemi di compatibilità sarebbero considerati bug da correggere. SignalR i client sono supportati nello stesso ciclo di vita del supporto come il resto di .NET Core. Per informazioni dettagliate, vedere [i criteri di supporto di .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Molte funzionalità richiedono un client **e** un server compatibili. Per una tabella che mostra le versioni minime per le varie funzionalità, vedere di seguito.

Le versioni 1. x di SignalR sono mappate alle versioni di .NET Core 2,1 e 2,2 e hanno la stessa durata. Per la versione 3. x e versioni successive, la SignalR versione corrisponde esattamente al resto di .NET e ha lo stesso ciclo di vita del supporto.

| Versione di SignalR | Versione di .NET Core | Livello di supporto | Fine del supporto |
| - | - | - | - |
| 1.0. x | 2.1.x | Supporto a lungo termine | 21 agosto 2021 |
| 1.1. x | 2.2. x | Fine vita | 23 dicembre 2019 |
| 3. x o superiore | *uguale a SignalR Version* | Vedere i [criteri di supporto di .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Nota:** In ASP.NET Core 3,0 il client JavaScript è stato *spostato* nel `@microsoft/signalr` pacchetto NPM.

## <a name="feature-distribution"></a>Distribuzione di funzionalità

La tabella seguente illustra le funzionalità e il supporto per i client che offrono il supporto in tempo reale. Per ogni funzionalità viene elencata la versione *minima* supportata per questa funzionalità. Se non è elencata alcuna versione, la funzionalità non è supportata.

| Funzionalità | Server | Client .NET | Client JavaScript | Client Java |
| ---- | :-: | :-: | :-: | :-: |
| Supporto per i servizi di Azure SignalR |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming da server a client](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming da client a server](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Riconnessione automatica ([.NET](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection), [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Trasporto WebSocket |2.1.0|1.0.0|1.0.0|1.0.0|
| Trasporto eventi Server-Sent |2.1.0|1.0.0|1.0.0|❌|
| Trasporto di polling prolungato |2.1.0|1.0.0|1.0.0|3.0.0|
| Protocollo dell'hub JSON |2.1.0|1.0.0|1.0.0|1.0.0|
| Protocollo hub MessagePack |2.1.0|1.0.0|1.0.0|❌|

Il supporto per l'abilitazione di funzionalità client aggiuntive viene registrato nello strumento di [rilevamento dei problemi](https://github.com/dotnet/AspNetCore/issues).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Introduzione a SignalR per ASP.NET Core](xref:tutorials/signalr)
* [Piattaforme supportate](xref:signalr/supported-platforms)
* [Hub](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)