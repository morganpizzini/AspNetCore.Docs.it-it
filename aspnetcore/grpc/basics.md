---
title: Servizi gRPC con C#
author: juntaoluo
description: Informazioni sui concetti di base per la scrittura di servizi gRPC con C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061015"
---
# <a name="grpc-services-with-c"></a>Servizi gRPC con C\#

Questo documento illustra i concetti necessari per scrivere app [gRPC](https://grpc.io/docs/guides/) in C#. Gli argomenti trattati in questo articolo si applicano alle app gRPC basate su [C-core](https://grpc.io/blog/grpc-stacks)e su ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>file proto

gRPC usa un approccio basato sul contratto per lo sviluppo di API. I buffer di protocollo (protobuf) vengono usati per impostazione predefinita come IDL (Interface Definition Language). Il file con *\* estensione proto* contiene:

* Definizione del servizio gRPC.
* Messaggi inviati tra client e server.

Per ulteriori informazioni sulla sintassi dei file protobuf, vedere <xref:grpc/protobuf> .

Si consideri ad esempio il file *greet. proto* usato in [Introduzione al servizio gRPC](xref:tutorials/grpc/grpc-start):

* Definisce un `Greeter` servizio.
* Il `Greeter` servizio definisce una `SayHello` chiamata.
* `SayHello` Invia un `HelloRequest` messaggio e riceve un `HelloReply` messaggio:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Aggiungere un file. proto a un' \# app C

Il file con *\* estensione proto* è incluso in un progetto aggiungendolo al `<Protobuf>` gruppo di elementi:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Per impostazione predefinita, un `<Protobuf>` riferimento genera un client concreto e una classe di base del servizio. L'attributo dell'elemento di riferimento `GrpcServices` può essere usato per limitare la generazione di asset in C#. Le `GrpcServices` Opzioni valide sono:

* `Both` (impostazione predefinita quando non è presente)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>Supporto degli strumenti C# per i file. proto

Il pacchetto di strumenti [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) è necessario per generare gli asset C# da file *\* . proto* . Asset generati (file):

* Vengono generati ogni volta che il progetto viene compilato in base alle esigenze.
* Non vengono aggiunti al progetto o archiviati nel controllo del codice sorgente.
* È un artefatto di compilazione contenuto nella directory *obj* .

Questo pacchetto è necessario per i progetti server e client. Il `Grpc.AspNetCore` metapacchetto include un riferimento a `Grpc.Tools` . I progetti server possono `Grpc.AspNetCore` essere aggiunti tramite Gestione pacchetti in Visual Studio o aggiungendo un `<PackageReference>` al file di progetto:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

I progetti client devono fare riferimento direttamente `Grpc.Tools` a insieme agli altri pacchetti necessari per usare il client gRPC. Il pacchetto di strumenti non è necessario in fase di esecuzione, quindi la dipendenza è contrassegnata con `PrivateAssets="All"` :

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Asset C# generati

Il pacchetto di strumenti genera i tipi C# che rappresentano i messaggi definiti nei file con *\* estensione proto* inclusi.

Per gli asset lato server, viene generato un tipo di base del servizio abstract. Il tipo di base contiene le definizioni di tutte le chiamate gRPC contenute nel file *. proto* . Creare un'implementazione del servizio concreta che deriva da questo tipo di base e implementa la logica per le chiamate gRPC. Per `greet.proto` , l'esempio descritto in precedenza, `GreeterBase` viene generato un tipo astratto che contiene un `SayHello` metodo virtuale. Un'implementazione concreta `GreeterService` esegue l'override del metodo e implementa la logica di gestione della chiamata gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Per gli asset lato client viene generato un tipo di client concreto. Le chiamate gRPC nel file *. proto* vengono convertite in metodi sul tipo concreto, che può essere chiamato. Per l' `greet.proto` esempio descritto in precedenza, `GreeterClient` viene generato un tipo concreto. Chiamare `GreeterClient.SayHelloAsync` per avviare una chiamata gRPC al server.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Per impostazione predefinita, gli asset server e client vengono generati per ogni file con *\* estensione proto* incluso nel `<Protobuf>` gruppo di elementi. Per assicurarsi che vengano generati solo gli asset server in un progetto server, l' `GrpcServices` attributo è impostato su `Server` .

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Analogamente, l'attributo viene impostato su `Client` nei progetti client.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
