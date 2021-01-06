---
title: Servizi e client gRPC Code-First con .NET
author: jamesnk
description: Informazioni sui concetti di base per la scrittura di gRPC Code-First con .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880635"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>Servizi e client gRPC Code-First con .NET

Di [James Newton-King](https://twitter.com/jamesnk) e [Marc ghiaial](https://twitter.com/marcgravell)

GRPC Code-First usa i tipi .NET per definire contratti di servizio e di messaggio.

Code-First è una scelta ottimale quando un intero sistema USA .NET:

* Il servizio .NET e i tipi di contratto dati possono essere condivisi tra il server .NET e i client.
* Evita la necessità di definire contratti nei `.proto` file e nella generazione del codice.

Code-First non è consigliato nei sistemi poliglotti con più lingue. Il servizio .NET e i tipi di contratto dati non possono essere usati con le piattaforme non-.NET. Per chiamare un servizio gRPC scritto usando Code-First, altre piattaforme devono creare un `.proto` contratto che corrisponda al servizio.

## <a name="protobuf-netgrpc"></a>protobuf-net. Grpc

> [!IMPORTANT]
> Per informazioni su protobuf-net. Grpc, visitare il [protobuf-net. Sito Web Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) o creare un problema in [protobuf-net. Repository GitHub Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net. Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) è un progetto della community e non è supportato da Microsoft. Viene aggiunto il supporto del primo codice a `Grpc.AspNetCore` e `Grpc.Net.Client` . USA i tipi .NET annotati con gli attributi per definire i servizi e i messaggi di gRPC di un'app.

Il primo passaggio per la creazione di un servizio gRPC Code-First consiste nel definire il contratto di codice:

* Creare un nuovo progetto che verrà condiviso dal server e dal client.
* Aggiungere un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Riferimento al pacchetto Grpc.
* Creare i tipi di contratto di servizio e dati.

[!code-csharp[](code-first/Contracts.cs)]

Il codice precedente:

* Definisce `HelloRequest` `HelloReply` i messaggi e.
* Definisce l' `IGreeterService` interfaccia del contratto con il `SayHelloAsync` Metodo unario gRPC.

Il contratto di servizio viene implementato nel server e viene chiamato dal client. I metodi definiti nelle interfacce del servizio devono corrispondere a determinate firme a seconda che si tratti di un flusso unario, di streaming del server, di streaming client o bidirezionale.

Per ulteriori informazioni sulla definizione dei contratti di servizio, vedere [protobuf-net. Documentazione introduttiva di Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Creare un servizio gRPC Code-First

Per aggiungere il servizio gRPC Code-First a un'app ASP.NET Core:

* Aggiungere un [protobuf-net. Riferimento al pacchetto Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .
* Aggiungere un riferimento al progetto del contratto di codice condiviso.

Creare un nuovo `GreeterService.cs` file e implementare l' `IGreeterService` interfaccia del servizio:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Aggiornare il `Startup.cs` file:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

Nel codice precedente:

* `AddCodeFirstGrpc` registra i servizi che consentono la prima codifica.
* `MapGrpcService<GreeterService>` aggiunge l'endpoint del servizio Code-First.

i servizi gRPC implementati con Code-First e `.proto` i file possono coesistere nella stessa app. Tutti i servizi gRPC usano la [configurazione del servizio gRPC](xref:grpc/configuration#configure-services-options).

## <a name="create-a-code-first-grpc-client"></a>Creare un client gRPC Code-First

Un client gRPC Code-First usa il contratto di servizio per chiamare i servizi di gRPC. Per chiamare un servizio gRPC usando un client Code-First:

* Aggiungere un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Riferimento al pacchetto Grpc.
* Aggiungere un riferimento al progetto del contratto di codice condiviso.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Il codice precedente:

* Crea un canale gRPC.
* Crea un client del primo codice dal canale con il `CreateGrpcService<IGreeterService>` metodo di estensione.
* Chiama il servizio gRPC con `SayHelloAsync` .

Un client gRPC Code-First viene creato da un canale. Proprio come un normale client, un client Code-First usa la relativa [configurazione del canale](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Risorse aggiuntive

* [protobuf-net. Sito Web Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net. Repository GitHub Grpc](https://github.com/protobuf-net/protobuf-net.Grpc)
