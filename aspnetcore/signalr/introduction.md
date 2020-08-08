---
title: Introduzione a ASP.NET CoreSignalR
author: bradygaster
description: Scopri come la SignalR libreria ASP.NET Core semplifica l'aggiunta di funzionalità in tempo reale alle app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022173"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>Introduzione a ASP.NET CoreSignalR

## <a name="what-is-no-locsignalr"></a>Che cos'è SignalR ?

ASP.NET Core SignalR è una libreria open source che semplifica l'aggiunta di funzionalità Web in tempo reale alle app. La funzionalità Web in tempo reale consente di eseguire il push del contenuto ai client immediatamente dal codice lato server.

Candidati validi per SignalR :

* App che richiedono aggiornamenti con frequenza elevata dal server, ad esempio giochi, social network, mappe, aste e app GPS e di voto.
* Dashboard e app di monitoraggio, ad esempio dashboard aziendali, aggiornamenti di vendite istantanee o avvisi di viaggio.
* App di collaborazione, ad esempio app per lavagne e software per riunioni in team.
* App che richiedono notifiche. Social network, posta elettronica, chat, giochi, avvisi di viaggio e molte altre app usano le notifiche.

SignalRfornisce un'API per la creazione di [chiamate a procedure remote (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)da server a client. Le chiamate RPC chiamano funzioni JavaScript nei client dal codice .NET Core lato server.

Di seguito sono riportate alcune funzionalità di SignalR per ASP.NET Core:

* Gestisce automaticamente la gestione delle connessioni.
* Invia messaggi contemporaneamente a tutti i client connessi. Ad esempio, una chat room.
* Invia messaggi a client o gruppi di client specifici.
* Ridimensiona per gestire l'aumento del traffico.

L'origine è ospitata in un [ SignalR repository in GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).

## <a name="transports"></a>Trasporti

SignalRsupporta le tecniche seguenti per la gestione della comunicazione in tempo reale (in ordine di fallback normale):

* [WebSocket](https://tools.ietf.org/html/rfc7118)
* Eventi inviati dal server
* Polling prolungato

SignalRsceglie automaticamente il metodo di trasporto migliore all'interno delle funzionalità del server e del client.

## <a name="hubs"></a>Hub

SignalRUSA gli *Hub* per la comunicazione tra client e server.

Un hub è una pipeline di alto livello che consente a un client e a un server di chiamare i metodi tra loro. SignalRgestisce automaticamente l'invio tra i limiti del computer, consentendo ai client di chiamare i metodi sul server e viceversa. È possibile passare parametri fortemente tipizzati ai metodi, che Abilita l'associazione di modelli. SignalRin sono disponibili due protocolli Hub predefiniti: un protocollo di testo basato su JSON e un protocollo binario basato su [MessagePack](https://msgpack.org/).  MessagePack crea in genere messaggi più piccoli rispetto a JSON. I browser meno recenti devono supportare il [livello XHR 2](https://caniuse.com/#feat=xhr2) per fornire il supporto del protocollo MessagePack.

Gli hub chiamano il codice lato client inviando messaggi che contengono il nome e i parametri del metodo lato client. Gli oggetti inviati come parametri del metodo vengono deserializzati utilizzando il protocollo configurato. Il client tenta di trovare una corrispondenza tra il nome e un metodo nel codice lato client. Quando il client trova una corrispondenza, chiama il metodo e passa ai dati dei parametri deserializzati.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Introduzione a SignalR per ASP.NET Core](xref:tutorials/signalr)
* [Piattaforme supportate](xref:signalr/supported-platforms)
* [Hub](xref:signalr/hubs)
* [Client JavaScript](xref:signalr/javascript-client)
