---
title: configurazione di gRPC per .NET
author: jamesnk
description: Informazioni su come configurare gRPC per le app .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 02/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/configuration
ms.openlocfilehash: 6e4259b538d32490d5281f189a04ab5a04dbcce5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774691"
---
# <a name="grpc-for-net-configuration"></a>configurazione di gRPC per .NET

## <a name="configure-services-options"></a>Configurare le opzioni dei servizi

i servizi gRPC sono configurati con `AddGrpc` in *Startup.cs*. Nella tabella seguente vengono descritte le opzioni per la configurazione dei servizi gRPC:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Dimensione massima in byte del messaggio che può essere inviata dal server. Il tentativo di inviare un messaggio che supera le dimensioni massime configurate per i messaggi comporta un'eccezione. Se impostato su `null`, le dimensioni del messaggio sono illimitate. |
| MaxReceiveMessageSize | 4 MB | Dimensione massima in byte del messaggio che può essere ricevuto dal server. Se il server riceve un messaggio che supera questo limite, viene generata un'eccezione. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. Se impostato su `null`, le dimensioni del messaggio sono illimitate. |
| EnableDetailedErrors | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo del servizio. Il valore predefinito è `false`. L' `EnableDetailedErrors` impostazione `true` di su può infiltrare le informazioni riservate. |
| CompressionProviders | gzip | Raccolta di provider di compressione utilizzati per comprimere e decomprimere i messaggi. I provider di compressione personalizzati possono essere creati e aggiunti alla raccolta. I provider configurati predefiniti supportano la compressione **gzip** . |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Algoritmo di compressione usato per comprimere i messaggi inviati dal server. L'algoritmo deve corrispondere a un provider di `CompressionProviders`compressione in. Per comprimere una risposta, il client deve indicare che supporta l'algoritmo inviando l'algoritmo nell'intestazione **grpc-Accept-Encoding** . |
| ResponseCompressionLevel | `null` | Livello di compressione utilizzato per comprimere i messaggi inviati dal server. |
| Intercettori | nessuno | Raccolta di intercettori eseguiti con ogni chiamata gRPC. Gli intercettori vengono eseguiti nell'ordine in cui sono registrati. Gli intercettori configurati a livello globale vengono eseguiti prima degli intercettori configurati per un singolo servizio. Per ulteriori informazioni sugli intercettori gRPC, vedere [GRPC Interceptors vs. middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |

È possibile configurare le opzioni per tutti i servizi fornendo le `AddGrpc` opzioni delegate alla chiamata in `Startup.ConfigureServices`:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Le opzioni per un singolo servizio eseguono l'override delle opzioni `AddGrpc` globali fornite in e possono `AddServiceOptions<TService>`essere configurate utilizzando:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Configurare le opzioni client

la configurazione del client gRPC è `GrpcChannelOptions`impostata su on. Nella tabella seguente vengono descritte le opzioni per la configurazione dei canali gRPC:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| HttpClient | Nuova istanza | Oggetto `HttpClient` utilizzato per eseguire chiamate gRPC. Un client può essere impostato per configurare un oggetto `HttpClientHandler`personalizzato o aggiungere ulteriori gestori alla pipeline HTTP per le chiamate gRPC. Se non `HttpClient` viene specificato alcun valore, viene `HttpClient` creata una nuova istanza per il canale. Verrà eliminato automaticamente. |
| DisposeHttpClient | `false` | Se `true`e si specifica `HttpClient` un oggetto, l' `HttpClient` istanza verrà eliminata quando `GrpcChannel` viene eliminato. |
| LoggerFactory | `null` | `LoggerFactory` Utilizzato dal client per registrare le informazioni sulle chiamate gRPC. Un' `LoggerFactory` istanza può essere risolta dall'inserimento delle dipendenze `LoggerFactory.Create`o creata utilizzando. Per esempi di configurazione della registrazione, <xref:grpc/diagnostics#grpc-client-logging>vedere. |
| MaxSendMessageSize | `null` | Dimensione massima in byte del messaggio che può essere inviato dal client. Il tentativo di inviare un messaggio che supera le dimensioni massime configurate per i messaggi comporta un'eccezione. Se impostato su `null`, le dimensioni del messaggio sono illimitate. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Dimensione massima del messaggio, in byte, che può essere ricevuta dal client. Se il client riceve un messaggio che supera questo limite, viene generata un'eccezione. L'aumento di questo valore consente al client di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. Se impostato su `null`, le dimensioni del messaggio sono illimitate. |
| Credenziali | `null` | Istanza di `ChannelCredentials`. Le credenziali vengono usate per aggiungere i metadati di autenticazione alle chiamate gRPC. |
| CompressionProviders | gzip | Raccolta di provider di compressione utilizzati per comprimere e decomprimere i messaggi. I provider di compressione personalizzati possono essere creati e aggiunti alla raccolta. I provider configurati predefiniti supportano la compressione **gzip** . |

Il codice seguente:

* Imposta la dimensione massima del messaggio di invio e ricezione sul canale.
* Crea un client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
