---
title: Introduzione a gRPC in .NET CoreIntroduction to gRPC on .NET Core
author: juntaoluo
description: Informazioni sui servizi gRPC con il server Kestrel e lo stack di ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667314"
---
# <a name="introduction-to-grpc-on-net-core"></a>Introduzione a gRPC in .NET CoreIntroduction to gRPC on .NET Core

Di [John Luo](https://github.com/juntaoluo) e [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) è un framework RPC indipendente dal linguaggio a elevate prestazioni.

I principali vantaggi di gRPC sono:
* Framework RPC moderno, ad alte prestazioni e leggero.
* Sviluppo di API con priorità al contratto usando i buffer del protocollo per impostazione predefinita e implementazioni indipendenti dal linguaggio.
* Strumenti disponibili per molte linguaggi consentono di generare client e server fortemente tipizzati.
* Supporto per chiamate client, server e di streaming bidirezionale.
* Utilizzo di rete ridotto grazie alla serializzazione binaria Protobuf.

Questi vantaggi rendono gRPC ideale per:
* Microservizi leggeri in cui l'efficienza è fondamentale.
* Sistemi poliglotti che richiedono l'uso di più linguaggi per lo sviluppo.
* Servizi in tempo reale da punto a punto che devono gestire richieste o risposte di streaming.

## <a name="c-tooling-support-for-proto-files"></a>Supporto degli strumenti di C

gRPC utilizza un approccio contract-first allo sviluppo di API. I servizi e i messaggi sono definiti in * \*file .proto:*

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

I tipi .NET per servizi, client * \** e messaggi vengono generati automaticamente includendo i file proto in un progetto:

* Aggiungere un riferimento al pacchetto [Grpc.Tools.Add](https://www.nuget.org/packages/Grpc.Tools/) a package reference to Grpc.Tools package.
* Aggiungere * \** file con estensione `<Protobuf>` proto al gruppo di elementi.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Per ulteriori informazioni sul supporto degli <xref:grpc/basics>strumenti gRPC, vedere .

## <a name="grpc-services-on-aspnet-core"></a>Servizi gRPC su ASP.NET Core

I servizi gRPC possono essere ospitati su ASP.NET Core. I servizi hanno una piena integrazione con le funzionalità di ASP.NET Core, come la registrazione, l'inserimento delle dipendenze (DI), l'autenticazione e l'autorizzazione.

Il modello di progetto di servizio gRPC fornisce un servizio di avvio:The gRPC service project template provides a starter service:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService`eredita dal `GreeterBase` tipo, generato dal `Greeter` servizio nel file * \*proto.* Il servizio è reso accessibile ai clienti in *Startup.cs:*

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Per ulteriori informazioni sui servizi gRPC <xref:grpc/aspnetcore>su ASP.NET Core, vedere .

## <a name="call-grpc-services-with-a-net-client"></a>Chiamare servizi gRPC con un client .NET

I client gRPC sono tipi di client concreti [generati da * \** file con estensione proto](xref:grpc/basics#generated-c-assets). Il client gRPC concreto dispone di metodi che si traducono nel servizio gRPC nel file * \*proto.*

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Un client gRPC viene creato utilizzando un canale, che rappresenta una connessione di lunga durata a un servizio gRPC. Un canale può `GrpcChannel.ForAddress`essere creato utilizzando .

Per ulteriori informazioni sulla creazione di client <xref:grpc/client>e sulla chiamata di metodi di servizio diversi, vedere .

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
