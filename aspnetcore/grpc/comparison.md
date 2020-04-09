---
title: Confrontare servizi gRPC e API HTTP
author: jamesnk
description: Scopri come gRPC si confronta con le API HTTP e quali sono gli scenari consigliati.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
no-loc:
- SignalR
uid: grpc/comparison
ms.openlocfilehash: 2dff64f1f2d67b8a1e676acf6cf131b684099750
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405874"
---
# <a name="compare-grpc-services-with-http-apis"></a>Confrontare servizi gRPC e API HTTP

Di [James Newton-King](https://twitter.com/jamesnk)

In questo articolo viene illustrato il confronto tra i [servizi gRPC](https://grpc.io/docs/guides/) e le API HTTP (incluse [le API Web](xref:web-api/index)di ASP.NET Core). La tecnologia utilizzata per fornire un'API per la tua app è una scelta importante e gRPC offre vantaggi unici rispetto alle API HTTP. In questo articolo vengono illustrati i punti di forza e di debolezza di gRPC e vengono consigliati scenari per l'utilizzo di gRPC rispetto ad altre tecnologie.

## <a name="high-level-comparison"></a>Confronto di alto livello

La tabella seguente offre un confronto generale delle funzionalità tra le API gRPC e HTTP con JSON.

| Funzionalità          | gRPC                                               | HTTP APIs with JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Contratto         | Obbligatorio (*.proto*)                                | Facoltativo (OpenAPI)            |
| Protocollo         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (piccolo, binario)](#performance)           | JSON (grande, leggibile)  |
| Prescrittività | [Specifiche rigorose](#strict-specification)      | Sciolto. Qualsiasi HTTP è valido.     |
| Streaming        | [Client, server, bidirezionale](#streaming)       | Client, server                |
| Supporto browser  | [No (richiede grpc-web)](#limited-browser-support) | Sì                           |
| Sicurezza         | Trasporto (TLS)                                    | Trasporto (TLS)               |
| Generazione di codice client | [Sì](#code-generation)                      | OpenAPI - utensili di terze parti |

## <a name="grpc-strengths"></a>gRPC punti di forza

### <a name="performance"></a>Prestazioni

I messaggi gRPC vengono serializzati utilizzando [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), un formato di messaggio binario efficiente. Protobuf serializza molto rapidamente sul server e sul client. La serializzazione di protobuf genera payload di messaggi di piccole dimensioni, importanti in scenari con larghezza di banda limitata come le app per dispositivi mobili.

gRPC è progettato per HTTP/2, una revisione importante di HTTP che offre vantaggi significativi in termini di prestazioni rispetto a HTTP 1.x:

* Inquadratura e compressione binarie. Il protocollo HTTP/2 è compatto ed efficiente sia nell'invio che nella ricezione.
* Multiplexing di più chiamate HTTP/2 su una singola connessione TCP. Il multiplexing elimina il [blocco head-of-line](https://en.wikipedia.org/wiki/Head-of-line_blocking).

### <a name="code-generation"></a>Generazione del codice

Tutti i framework gRPC forniscono supporto di prima classe per la generazione di codice. Un file di base per lo sviluppo gRPC è il [file .proto](https://developers.google.com/protocol-buffers/docs/proto3), che definisce il contratto di servizi e messaggi gRPC. Da questo file gRPC framework verranno codificati generano una classe di base del servizio, messaggi e un client completo.

Condividendo il file *proto* tra il server e il client, i messaggi e il codice client possono essere generati dall'estremità alla fine. La generazione del codice del client elimina la duplicazione dei messaggi sul client e sul server e crea automaticamente un client fortemente tipizzato. Non dover scrivere un client consente di risparmiare tempi di sviluppo significativi nelle applicazioni con molti servizi.

### <a name="strict-specification"></a>Specifiche rigorose

Non esiste una specifica formale per l'API HTTP con JSON. Gli sviluppatori discutono il formato migliore di URL, verbi HTTP e codici di risposta.

La [specifica gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) è prescrittiva sul formato che un servizio gRPC deve seguire. gRPC elimina il dibattito e fa risparmiare tempo agli sviluppatori perché gRPC è coerente tra piattaforme e implementazioni.

### <a name="streaming"></a>Streaming

HTTP/2 fornisce una base per flussi di comunicazione in tempo reale di lunga durata. gRPC fornisce supporto di prima classe per lo streaming tramite HTTP/2.

Un servizio gRPC supporta tutte le combinazioni di streaming:A gRPC service supports all streaming combinations:

* Unario (senza streaming)
* Streaming da server a client
* Streaming da client a server
* Streaming bidirezionale

### <a name="deadlinetimeouts-and-cancellation"></a>Scadenza/timeout e cancellazione

gRPC consente ai client di specificare per quanto tempo sono disposti ad attendere il completamento di una RPC. La [scadenza](https://grpc.io/blog/deadlines) viene inviata al server e il server può decidere l'azione da intraprendere se supera la scadenza. Ad esempio, il server potrebbe annullare le richieste in corso gRPC/HTTP/database al timeout.

La propagazione della scadenza e dell'annullamento tramite chiamate gRPC figlio consente di applicare i limiti di utilizzo delle risorse.

## <a name="grpc-recommended-scenarios"></a>Scenari consigliati gRPC

gRPC è adatto ai seguenti scenari:

* **Microservizi** &ndash; gRPC è progettato per la bassa latenza e la comunicazione ad alta velocità effettiva. gRPC è ideale per microservizi leggeri in cui l'efficienza è fondamentale.
* **Comunicazione point-to-point** &ndash; in tempo reale gRPC ha un eccellente supporto per lo streaming bidirezionale. I servizi gRPC possono eseguire il push dei messaggi in tempo reale senza polling.
* **Ambienti poliglotta** &ndash; gRPC strumenti supporta tutti i linguaggi di sviluppo popolari, rendendo gRPC una buona scelta per gli ambienti multilingue.
* **Ambienti vincolati** &ndash; di rete i messaggi gRPC vengono serializzati con Protobuf, un formato di messaggio leggero. Un messaggio gRPC è sempre più piccolo di un messaggio JSON equivalente.

## <a name="grpc-weaknesses"></a>Punti deboli gRPC

### <a name="limited-browser-support"></a>Supporto limitato del browser

È impossibile chiamare direttamente un servizio gRPC da un browser oggi. gRPC utilizza pesantemente le funzionalità HTTP/2 e nessun browser fornisce il livello di controllo richiesto sulle richieste Web per supportare un client gRPC. Ad esempio, i browser non consentono a un chiamante di richiedere l'utilizzo di HTTP/2 o di fornire l'accesso ai frame HTTP/2 sottostanti.

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) è una tecnologia aggiuntiva del team gRPC che fornisce un supporto gRPC limitato nel browser. gRPC-Web è costituito da due parti: un client JavaScript che supporta tutti i browser moderni e un proxy gRPC-Web sul server. Il client gRPC-Web chiama il proxy e il proxy verrà inoltrato sulle richieste gRPC al server gRPC.

Non tutte le funzionalità di gRPC sono supportate da gRPC-Web. Lo streaming client e bidirezionale non è supportato ed è un supporto limitato per lo streaming server.

> [!TIP]
> .NET Core ha il supporto sperimentale per gRPC-Web. Visita <xref:grpc/browser> per ulteriori informazioni.

### <a name="not-human-readable"></a>Non leggibile dall'uomo

Le richieste API HTTP vengono inviate come testo e possono essere lette e create dagli utenti.

I messaggi gRPC sono codificati con Protobuf per impostazione predefinita. Mentre Protobuf è efficiente da inviare e ricevere, il suo formato binario non è leggibile dall'uomo. Protobuf richiede che la descrizione dell'interfaccia del messaggio specificata nel file *proto* deserializzi correttamente. Ulteriori strumenti sono necessari per analizzare i payload Protobuf sul filo e per comporre le richieste a mano.

Funzionalità come la [reflection del server](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) e lo strumento da riga di comando [gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) esistono per facilitare i messaggi binari Protobuf. Inoltre, i messaggi Protobuf supportano la [conversione da e verso JSON.](https://developers.google.com/protocol-buffers/docs/proto3#json) La conversione JSON incorporata fornisce un modo efficiente per convertire i messaggi Protobuf da e verso la forma leggibile durante il debug.

## <a name="alternative-framework-scenarios"></a>Scenari di framework alternativi

Altri framework sono consigliati su gRPC nei seguenti scenari:

* **API accessibili dal** &ndash; browser gRPC non è completamente supportato nel browser. gRPC-Web può offrire il supporto del browser, ma ha limitazioni e introduce un proxy server.
* **La trasmissione** &ndash; della comunicazione in tempo reale gRPC supporta la comunicazione in tempo reale tramite streaming, ma il concetto di trasmettere un messaggio alle connessioni registrate non esiste. Ad esempio, in uno scenario di chat room in cui i nuovi messaggi di chat devono essere inviati a tutti i client nella chat room, ogni chiamata gRPC è necessaria per trasmettere singolarmente nuovi messaggi di chat al client. [SignalR](xref:signalr/introduction)è un framework utile per questo scenario. SignalRha il concetto di connessioni persistenti e supporto integrato per la trasmissione di messaggi.
* **Comunicazione tra processi** &ndash; Un processo deve ospitare un server HTTP/2 per accettare le chiamate gRPC in arrivo. Per Windows, le [pipe](/dotnet/standard/io/pipe-operations) di comunicazione tra processi sono un metodo di comunicazione rapido e leggero.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
