---
title: Testare i servizi gRPC con gRPCurl in ASP.NET Core
author: jamesnk
description: Informazioni su come testare i servizi con gli strumenti gRPC. gRPCurl uno strumento da riga di comando per l'interazione con i servizi di gRPC. gRPCui è un'interfaccia utente Web interattiva.
monikerRange: '>= aspnetcore-3.1'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: 15652431ea4bebc879af4c57667cbf854c49330c
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721820"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Testare i servizi gRPC con gRPCurl in ASP.NET Core

Di [James Newton-King](https://twitter.com/jamesnk)

Gli strumenti sono disponibili per gRPC che consentono agli sviluppatori di testare i servizi senza creare app client:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) è uno strumento da riga di comando che consente di interagire con i servizi di gRPC.
* [gRPCui](https://github.com/fullstorydev/grpcui) si basa su gRPCurl e aggiunge un'interfaccia utente Web interattiva per gRPC, in modo analogo a strumenti come subspettatore e interfaccia utente di spavalderia.

Questo articolo illustra come:

* Scaricare e installare gRPCurl e gRPCui.
* Configurare la reflection gRPC con un'app gRPC ASP.NET Core.
* Individuare e testare i servizi gRPC con `grpcurl` .
* Interagire con i servizi gRPC tramite un browser usando `grpcui` .

## <a name="about-grpcurl"></a>Informazioni su gRPCurl

gRPCurl è uno strumento da riga di comando creato dalla community gRPC. Le funzionalità includono:

* Chiamata dei servizi gRPC, inclusi i servizi di streaming.
* Individuazione del servizio mediante [Reflection gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).
* Elenco e descrizione dei servizi gRPC.
* Funziona con server protetti (TLS) e non sicuri (testo normale).

Per informazioni sul download e l'installazione di `grpcurl` , vedere la [Home page di GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).

![riga di comando di gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Configurare la reflection gRPC

`grpcurl` deve essere a conoscenza del contratto protobuf dei servizi prima che possa chiamarli. A questo scopo è possibile procedere in due modi:

* Configurare la [Reflection gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) nel server. gRPCurl individua automaticamente i contratti di servizio.
* Specificare `.proto` i file negli argomenti della riga di comando per gRPCurl.

L'uso di gRPCurl con la reflection gRPC è più semplice. gRPC Reflection aggiunge un nuovo servizio gRPC all'app che i client possono chiamare per individuare i servizi.

gRPC ASP.NET Core dispone del supporto incorporato per la reflection gRPC con il [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pacchetto. Per configurare la reflection in un'app:

* Aggiungere un `Grpc.AspNetCore.Server.Reflection` riferimento al pacchetto.
* Registra Reflection in `Startup.cs` :
  * `AddGrpcReflection` per registrare servizi che abilitano la reflection.
  * `MapGrpcReflectionService` per aggiungere un endpoint del servizio Reflection.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Quando la reflection gRPC è impostata:

* Un servizio di Reflection gRPC viene aggiunto all'app Server.
* Le app client che supportano la reflection gRPC possono chiamare il servizio reflection per individuare i servizi ospitati dal server.
* i servizi gRPC vengono ancora chiamati dal client. La reflection Abilita solo l'individuazione del servizio e non ignora la sicurezza lato server. Per gli endpoint protetti dall' [autenticazione e dall'autorizzazione](xref:grpc/authn-and-authz) è necessario che il chiamante passi le credenziali affinché l'endpoint venga chiamato correttamente.

## <a name="use-grpcurl"></a>Utilizzare `grpcurl`.

Nell' `-help` argomento vengono illustrate le `grpcurl` Opzioni della riga di comando:

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Individuare i servizi

Usare il `describe` verbo per visualizzare i servizi definiti dal server:

```powershell
> grpcurl.exe localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

L'esempio precedente:

* Esegue il `describe` verbo nel server `localhost:5001` .
* Stampa i servizi e i metodi restituiti dalla reflection gRPC.
  * `Greeter` è un servizio implementato dall'app.
  * `ServerReflection` servizio aggiunto dal `Grpc.AspNetCore.Server.Reflection` pacchetto.

Combinare `describe` con un servizio, un metodo o un nome di messaggio per visualizzarne i dettagli:

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Chiamare i servizi gRPC

Chiamare un servizio gRPC specificando un nome di servizio e di metodo insieme a un argomento JSON che rappresenta il messaggio di richiesta. Il codice JSON viene convertito in protobuf e inviato al servizio.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Nell'esempio precedente:

* L' `-d` argomento specifica un messaggio di richiesta con JSON. Questo argomento deve precedere l'indirizzo del server e il nome del metodo.
* Chiama il `SayHello` metodo sul `greeter.Greeter` servizio.
* Stampa il messaggio di risposta come JSON.

## <a name="about-grpcui"></a>Informazioni su gRPCui

gRPCui è un'interfaccia utente Web interattiva per gRPC. Si basa su gRPCurl e offre un'interfaccia utente grafica per l'individuazione e il testing dei servizi gRPC, in modo analogo agli strumenti HTTP come il postazione o l'interfaccia utente di spavalderia.

Per informazioni sul download e l'installazione di `grpcui` , vedere la [Home page di GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Uso di `grpcui`

Eseguire `grpcui` con l'indirizzo del server per interagire con come argomento:

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Lo strumento avvia una finestra del browser con l'interfaccia utente Web interattiva. i servizi gRPC vengono individuati automaticamente usando la reflection gRPC.

![interfaccia utente Web di gRPCuiri](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Home page di GitHub gRPCurl](https://github.com/fullstorydev/grpcurl)
* [Home page di GitHub gRPCui](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
