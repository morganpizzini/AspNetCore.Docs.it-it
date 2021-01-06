---
title: Perché eseguire la migrazione da WCF ad ASP.NET Core gRPC
author: markrendle
description: Questo articolo fornisce un riepilogo dei motivi per cui ASP.NET Core gRPC è una soluzione ideale per gli sviluppatori di Windows Communication Foundation (WCF) che desiderano eseguire la migrazione a architetture e piattaforme moderne.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058688"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC per sviluppatori Windows Communication Foundation (WCF)

Questo articolo fornisce un riepilogo dei motivi per cui ASP.NET Core gRPC è una soluzione ideale per gli sviluppatori di Windows Communication Foundation (WCF) che desiderano eseguire la migrazione a architetture e piattaforme moderne.

## <a name="comparison-to-wcf"></a>Confronto con WCF

Sebbene l'implementazione e l'approccio siano diversi per gRPC, l'esperienza di sviluppo e utilizzo di servizi con gRPC dovrebbe essere intuitiva per gli sviluppatori WCF. WCF e gRPC sono Framework RPC (Remote Procedure Call) con gli stessi obiettivi:

* È possibile scrivere codice come se il client e il server si trovino nella stessa piattaforma.
* Fornire un'API di rete portatile semplificata.

Entrambe le piattaforme condividono il requisito di dichiarare e implementare un'interfaccia, sebbene il processo di dichiarazione dell'interfaccia sia diverso. Molti tipi di chiamate RPC supportate da gRPC vengono mappate correttamente ai binding disponibili per i servizi WCF. Per ulteriori informazioni ed esempi, vedere [eseguire la migrazione di una soluzione WCF a gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Vantaggi di gRPC

gRPC fornisce un framework migliore rispetto ad altri approcci per i motivi seguenti.

### <a name="performance"></a>Prestazioni

gRPC Usa HTTP/2. Diversamente da HTTP/1.1, HTTP/2:

* È un protocollo binario più piccolo e più veloce.
* È più efficiente per l'analisi dei computer.
* Supporta le richieste di multiplexing su una singola connessione. Il multiplexing consente l'invio di più richieste su una connessione senza che le richieste si blocchino a vicenda. In HTTP/1.1 il blocco è noto come "blocco Head-of-line (HOL)".

gRPC utilizza protobuf, un formato binario efficiente, per serializzare i messaggi. I messaggi protobuf sono:
* Veloce da serializzare e deserializzare.
* Utilizzare una larghezza di banda inferiore rispetto ai formati basati su testo. 

gRPC è una soluzione efficace per dispositivi mobili e reti con restrizioni della larghezza di banda.

### <a name="interoperability"></a>Interoperabilità

Sono disponibili strumenti e librerie gRPC per tutti i principali linguaggi di programmazione e piattaforme, tra cui .NET, Java, Python, go, C++, Node.js, Swift, Dart, Ruby e PHP. Grazie al formato wire Binary protobuf e alla generazione efficiente del codice per ogni piattaforma, gli sviluppatori possono creare app multipiattaforma e ad alte prestazioni.

### <a name="usability-and-productivity"></a>Usabilità e produttività

gRPC è una soluzione RPC completa. Funziona in modo coerente in più linguaggi e piattaforme. Fornisce inoltre strumenti eccellenti, con la maggior parte del codice standard generato automaticamente. Analogamente a WCF, gRPC genera automaticamente messaggi e un client fortemente tipizzato. Il tempo degli sviluppatori viene liberato per concentrarsi sulla logica di business.

### <a name="streaming"></a>Streaming

gRPC dispone di un flusso bidirezionale completo, che fornisce funzionalità simili ai servizi duplex completi di WCF. il flusso di gRPC può funzionare su connessioni Internet normali, servizi di bilanciamento del carico e mesh dei servizi.

### <a name="deadlines-timeouts-and-cancellation"></a>Scadenze, timeout e annullamento

gRPC consente ai client di specificare il tempo massimo per il completamento di una RPC. Se viene superata la scadenza specificata, il server può annullare l'operazione indipendentemente dal client. Le scadenze e gli annullamenti possono essere propagati attraverso le successive chiamate gRPC per consentire l'applicazione dei limiti di utilizzo delle risorse. I client possono arrestare le operazioni quando viene superata una scadenza o in una versione precedente, se necessario. Ad esempio, i client possono arrestare le operazioni a causa di un'interazione dell'utente.

### <a name="security"></a>Sicurezza

gRPC può usare TLS e HTTP/2 per fornire una connessione crittografata end-to-end tra il client e il server. Il supporto per l'autenticazione dei certificati client aumenta ulteriormente la sicurezza e il trust tra client e server.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC come percorso di migrazione per WCF a .NET Core e .NET 5

.NET Core e .NET 5 contrassegnano un cambiamento nel modo in cui Microsoft offre soluzioni di comunicazione remota agli sviluppatori che desiderano distribuire servizi in una vasta gamma di piattaforme. .NET Core e .NET 5 supportano la [chiamata ai servizi WCF](/dotnet/core/additional-tools/wcf-web-service-reference-guide), ma non offrono il supporto lato server per l'hosting di WCF.

Esistono due percorsi consigliati per la modernizzazione delle app WCF:

* gRPC si basa su tecnologie moderne ed è emerso come la scelta più comune nella community di sviluppatori per le app RPC. A partire da .NET Core 3,0, le piattaforme .NET moderne offrono un supporto eccellente per gRPC. La migrazione dei servizi WCF per l'uso di gRPC consente di fornire le funzionalità RPC, le prestazioni, un'interoperabilità necessaria nelle app moderne.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) è uno sforzo della community per fornire supporto per l'hosting di servizi WCF a .NET Core e .NET 5. È disponibile una versione di anteprima e il progetto sta lavorando per prepararsi alla produzione. CoreWCF supporta solo un subset delle funzionalità di WCF e .NET Framework le app di cui è stata eseguita la migrazione per usarle saranno necessarie modifiche al codice e test per avere esito positivo. CoreWCF è una scelta ottimale se un'applicazione deve mantenere la compatibilità con i client esistenti che chiamano i servizi WCF.

## <a name="get-started"></a>Operazioni preliminari

Per istruzioni dettagliate sulla creazione di servizi gRPC in ASP.NET Core per gli sviluppatori WCF, vedere [ASP.NET Core gRPC per sviluppatori WCF](/dotnet/architecture/grpc-for-wcf-developers)
