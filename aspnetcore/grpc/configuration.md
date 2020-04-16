---
title: gRPC per la configurazione .NET
author: jamesnk
description: Scopri come configurare gRPC per le app .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
uid: grpc/configuration
ms.openlocfilehash: 4c13c45ce745643c3cb089a1c984d2ef599db48b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440831"
---
# <a name="grpc-for-net-configuration"></a>gRPC per la configurazione .NET

## <a name="configure-services-options"></a>Configurare le opzioni dei servizi

I servizi gRPC `AddGrpc` sono configurati con in *Startup.cs*. Nella tabella seguente vengono descritte le opzioni per la configurazione dei servizi gRPC:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| MaxSendMessageSize (Dimensioni Messaggio MaxSend) | `null` | Dimensione massima dei messaggi in byte che possono essere inviati dal server. Se si tenta di inviare un messaggio che supera la dimensione massima configurata, viene verificata una eccezione. Se impostato `null`su , la dimensione del messaggio è illimitata. |
| MaxReceiveMessageSize (Dimensioni Messaggio MaxReceiveMessageSize) | 4 MB | Dimensione massima dei messaggi in byte che possono essere ricevuti dal server. Se il server riceve un messaggio che supera questo limite, genera un'eccezione. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. Se impostato `null`su , la dimensione del messaggio è illimitata. |
| EnableDetailedErrors | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo di servizio. Il valore predefinito è `false`. L'impostazione `EnableDetailedErrors` su `true` può causare la perdita di informazioni riservate. |
| CompressionProviders (CompressionProviders) | gzip | Raccolta di provider di compressione utilizzati per comprimere e decomprimere i messaggi. I provider di compressione personalizzati possono essere creati e aggiunti alla raccolta. I provider configurati predefiniti supportano la compressione **gzip.** |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algoritmo di compressione utilizzato per comprimere i messaggi inviati dal server. L'algoritmo deve corrispondere `CompressionProviders`a un provider di compressione in . Affinché l'algoritmo comprime una risposta, il client deve indicare che supporta l'algoritmo inviandolo nell'intestazione **grpc-accept-encoding.** |
| ResponseCompressionLevel | `null` | Livello di compressione utilizzato per comprimere i messaggi inviati dal server. |
| Intercettori | nessuno | Raccolta di intercettori eseguiti con ogni chiamata gRPC. Gli intercettori vengono eseguiti nell'ordine in cui sono registrati. Gli intercettori configurati a livello globale vengono eseguiti prima degli intercettori configurati per un singolo servizio. Per ulteriori informazioni sugli intercettori gRPC, vedere [intercettori gRPC e Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

Le opzioni possono essere configurate per tutti `AddGrpc` i `Startup.ConfigureServices`servizi fornendo un delegato delle opzioni alla chiamata in:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Le opzioni per un singolo servizio `AddGrpc` sostituiscono le `AddServiceOptions<TService>`opzioni globali disponibili in e possono essere configurate utilizzando:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurare le opzioni client

La configurazione del client `GrpcChannelOptions`gRPC è impostata su . Nella tabella seguente vengono descritte le opzioni per la configurazione dei canali gRPC:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| HttpClient | Nuova istanza | Oggetto `HttpClient` utilizzato per effettuare chiamate gRPC. Un client può essere impostato `HttpClientHandler`per configurare un oggetto personalizzato o aggiungere gestori aggiuntivi alla pipeline HTTP per le chiamate gRPC. Se `HttpClient` non viene specificato `HttpClient` alcun valore, viene creata una nuova istanza per il canale. Verrà automaticamente eliminato. |
| DisposeHttpClient | `false` | Se `true`, `HttpClient` e viene specificato `HttpClient` un oggetto , `GrpcChannel` l'istanza verrà eliminata quando l'oggetto viene eliminato. |
| LoggerFactory (Fabbrica di logger) | `null` | Oggetto `LoggerFactory` utilizzato dal client per registrare le informazioni sulle chiamate gRPC. Un'istanza `LoggerFactory` può essere risolta dall'inserimento delle dipendenze o creata utilizzando `LoggerFactory.Create`. Per esempi di configurazione <xref:grpc/diagnostics#grpc-client-logging>della registrazione, vedere . |
| MaxSendMessageSize (Dimensioni Messaggio MaxSend) | `null` | Dimensione massima dei messaggi in byte che possono essere inviati dal client. Se si tenta di inviare un messaggio che supera la dimensione massima configurata, viene verificata una eccezione. Se impostato `null`su , la dimensione del messaggio è illimitata. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize (Dimensioni Messaggio MaxReceiveMessageSize)</span> | 4 MB | Dimensione massima dei messaggi in byte che possono essere ricevuti dal client. Se il client riceve un messaggio che supera questo limite, genera un'eccezione. L'aumento di questo valore consente al client di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. Se impostato `null`su , la dimensione del messaggio è illimitata. |
| Credenziali | `null` | Istanza di `ChannelCredentials`. Le credenziali vengono utilizzate per aggiungere metadati di autenticazione alle chiamate gRPC. |
| CompressionProviders (CompressionProviders) | gzip | Raccolta di provider di compressione utilizzati per comprimere e decomprimere i messaggi. I provider di compressione personalizzati possono essere creati e aggiunti alla raccolta. I provider configurati predefiniti supportano la compressione **gzip.** |

Il codice seguente:

* Imposta la dimensione massima dei messaggi di invio e ricezione sul canale.
* Crea un client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
