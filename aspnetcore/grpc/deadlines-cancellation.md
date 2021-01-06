---
title: Servizi gRPC affidabili con scadenze e annullamento
author: jamesnk
description: Informazioni su come creare servizi gRPC affidabili con scadenze e annullamento in .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059923"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Servizi gRPC affidabili con scadenze e annullamento

Di [James Newton-King](https://twitter.com/jamesnk)

Le scadenze e l'annullamento sono funzionalità usate dai client gRPC per interrompere le chiamate in corso. Questo articolo illustra il motivo per cui le scadenze e l'annullamento sono importanti e come usarle nelle app gRPC .NET.

## <a name="deadlines"></a>Scadenze

Una scadenza consente a un client gRPC di specificare il tempo di attesa per il completamento di una chiamata. Quando viene superata una scadenza, la chiamata viene annullata. L'impostazione di una scadenza è importante perché fornisce un limite superiore per il periodo di tempo per cui è possibile eseguire una chiamata. Si interrompe l'esecuzione di servizi non confunzionanti e l'esaurimento delle risorse del server. Le scadenze sono uno strumento utile per la creazione di app affidabili ed è necessario configurarle.

Configurazione scadenza:

* Una scadenza viene configurata utilizzando `CallOptions.Deadline` quando viene effettuata una chiamata.
* Non esiste alcun valore di scadenza predefinito. le chiamate gRPC non sono limitate all'ora, a meno che non venga specificata una scadenza.
* Una scadenza è l'ora UTC di quando viene superata la scadenza. Ad esempio, `DateTime.UtcNow.AddSeconds(5)` è una scadenza di 5 secondi a partire da ora.
* Se viene usata un'ora passata o corrente, la chiamata supera immediatamente la scadenza.
* La scadenza viene inviata con la chiamata gRPC al servizio e viene rilevata indipendentemente dal client e dal servizio. È possibile che una chiamata gRPC venga completata in un computer, ma nel momento in cui la risposta è stata restituita al client la scadenza è stata superata.

Se viene superata una scadenza, il client e il servizio hanno un comportamento diverso:

* Il client interrompe immediatamente la richiesta HTTP sottostante e genera un `DeadlineExceeded` errore. L'app client può scegliere di intercettare l'errore e visualizzare un messaggio di timeout per l'utente.
* Sul server, la richiesta HTTP in esecuzione viene interrotta e viene generato [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . Sebbene la richiesta HTTP venga interrotta, la chiamata gRPC continua a essere eseguita nel server fino al completamento del metodo. È importante che il token di annullamento venga passato ai metodi asincroni, in modo che vengano annullati insieme alla chiamata. Ad esempio, passando un token di annullamento a query asincrone del database e richieste HTTP. Il passaggio di un token di annullamento consente di completare rapidamente la chiamata annullata sul server e liberare risorse per altre chiamate.

Configurare `CallOptions.Deadline` per impostare una scadenza per una chiamata gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Uso `ServerCallContext.CancellationToken` di in un servizio gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Scadenze propagate

Quando viene effettuata una chiamata gRPC da un servizio gRPC in esecuzione, la scadenza deve essere propagata. Ad esempio:

1. L'app client chiama `FrontendService.GetUser` con una scadenza.
2. `FrontendService` chiama `UserService.GetUser`. La scadenza specificata dal client deve essere specificata con la nuova chiamata a gRPC.
3. `UserService.GetUser` riceve la scadenza. Si verifica un timeout corretto se viene superata la scadenza dell'app client.

Il contesto di chiamata fornisce la scadenza con `ServerCallContext.Deadline` :

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

La propagazione manuale delle scadenze può essere complessa. La scadenza deve essere passata a ogni chiamata ed è facile da perdere accidentalmente. Una soluzione automatica è disponibile con gRPC client Factory. Specifica `EnableCallContextPropagation` :

* Propaga automaticamente la scadenza e il token di annullamento alle chiamate figlio.
* È un ottimo modo per garantire che gli scenari gRPC complessi e nidificati propaghino sempre la scadenza e l'annullamento.

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Per altre informazioni, vedere <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Annullamento

L'annullamento consente a un client gRPC di annullare chiamate con esecuzione prolungata che non sono più necessarie. Ad esempio, una chiamata gRPC che trasmette gli aggiornamenti in tempo reale viene avviata quando l'utente visita una pagina in un sito Web. Il flusso deve essere annullato quando l'utente si sposta da una pagina all'altra.

Una chiamata gRPC può essere annullata nel client passando un token di annullamento con [CallOptions. CancellationToken](xref:System.Threading.CancellationToken) o chiamando `Dispose` sulla chiamata.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

i servizi gRPC che possono essere annullati devono:
* Passare `ServerCallContext.CancellationToken` a metodi asincroni. L'annullamento di metodi asincroni consente il completamento rapido della chiamata sul server.
* Propaga il token di annullamento alle chiamate figlio. La propagazione del token di annullamento garantisce che le chiamate figlio vengano annullate con il relativo elemento padre. [gRPC client Factory](xref:grpc/clientfactory) e `EnableCallContextPropagation()` propaga automaticamente il token di annullamento.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/client>
* <xref:grpc/clientfactory>
