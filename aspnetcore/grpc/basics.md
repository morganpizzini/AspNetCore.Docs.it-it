---
title: Servizi gRPC con C#
author: juntaoluo
description: Vengono illustrati i concetti di base relativi alla scrittura di servizi gRPC con C.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: 59257449e211ddf9c7faa5f21a3986caba4eebc6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664206"
---
# <a name="grpc-services-with-c"></a>Servizi gRPC con C\#

In questo documento vengono descritti i concetti necessari per scrivere app [gRPC](https://grpc.io/docs/guides/) in C. Gli argomenti trattati qui si applicano sia alle app gRPC basate su [C-core](https://grpc.io/blog/grpc-stacks)che a ASP.NET Core.

## <a name="proto-file"></a>proto file

gRPC utilizza un approccio contract-first allo sviluppo di API. I buffer di protocollo (protobuf) vengono utilizzati come IDL (Interface Design Language) per impostazione predefinita. Il file * \*proto* contiene:

* Definizione del servizio gRPC.
* Messaggi inviati tra client e server.

Per ulteriori informazioni sulla sintassi dei file protobuf, vedere la [documentazione ufficiale (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).

Si consideri, ad esempio, il file *greet.proto* utilizzato in [Introduzione al servizio gRPC](xref:tutorials/grpc/grpc-start):

* Definisce `Greeter` un servizio.
* Il `Greeter` servizio definisce una `SayHello` chiamata.
* `SayHello`invia `HelloRequest` un messaggio e `HelloReply` riceve un messaggio:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Aggiungere un file .proto\# a un'app C

Il * \** file proto viene incluso in un `<Protobuf>` progetto aggiungendolo al gruppo di elementi:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a>Supporto degli strumenti di C

Il pacchetto di strumenti [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) è necessario per generare le risorse di C' dai * \** file con estensione proto. Le risorse generate (file):

* Vengono generati in base alle esigenze ogni volta che il progetto viene compilato.
* Non vengono aggiunti al progetto o archiviati nel controllo del codice sorgente.
* Sono un elemento di compilazione contenuto nella directory *obj.*

Questo pacchetto è richiesto sia dai progetti server che da quelli client. Il `Grpc.AspNetCore` metapacchetto include `Grpc.Tools`un riferimento a . I progetti `Grpc.AspNetCore` server possono aggiungere usando Gestione pacchetti `<PackageReference>` in Visual Studio o aggiungendo un oggetto al file di progetto:Server projects can add using the Package Manager in Visual Studio or by adding a to the project file:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

I progetti client `Grpc.Tools` devono fare riferimento direttamente insieme agli altri pacchetti necessari per utilizzare il client gRPC. Il pacchetto di strumenti non è necessario in fase `PrivateAssets="All"`di esecuzione, pertanto la dipendenza è contrassegnata con :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Risorse di C 'A'generated

Il pacchetto di strumenti genera i tipi di C , che rappresentano i messaggi definiti nei file * \*con estensione proto* inclusi.

Per gli asset lato server, viene generato un tipo di base di servizio astratto. Il tipo di base contiene le definizioni di tutte le chiamate gRPC contenute nel file *proto.* Creare un'implementazione del servizio concreta che deriva da questo tipo di base e implementa la logica per le chiamate gRPC. Per `greet.proto`l'esempio descritto in `GreeterBase` precedenza, viene `SayHello` generato un tipo astratto contenente un metodo virtuale. Un'implementazione `GreeterService` concreta esegue l'override del metodo e implementa la logica che gestisce la chiamata gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Per le risorse lato client, viene generato un tipo di client concreto. Le chiamate gRPC nel file *proto* vengono convertite in metodi sul tipo concreto, che possono essere chiamati. Per `greet.proto`l'esempio descritto in `GreeterClient` precedenza, viene generato un tipo concreto. Chiamata `GreeterClient.SayHelloAsync` per avviare una chiamata gRPC al server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Per impostazione predefinita, le risorse server e client `<Protobuf>` vengono generate per ogni * \** file proto incluso nel gruppo di elementi. Per garantire che in un progetto server `GrpcServices` vengano generate `Server`solo le risorse del server, l'attributo è impostato su .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Analogamente, l'attributo è impostato su `Client` nei progetti client.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
