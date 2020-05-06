---
title: Introduzione a gRPC in .NET Core
author: juntaoluo
description: Informazioni sui servizi gRPC con il server Kestrel e lo stack di ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/index
ms.openlocfilehash: 2d7d683051fd1eb97f3f57d75bd582109166a6cd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768848"
---
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="25cd7-103">Introduzione a gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="25cd7-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="25cd7-104">Di [John Luo](https://github.com/juntaoluo) e [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="25cd7-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="25cd7-105">[gRPC](https://grpc.io/docs/guides/) è un framework RPC indipendente dal linguaggio a elevate prestazioni.</span><span class="sxs-lookup"><span data-stu-id="25cd7-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="25cd7-106">I principali vantaggi di gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="25cd7-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="25cd7-107">Framework RPC moderno, ad alte prestazioni e leggero.</span><span class="sxs-lookup"><span data-stu-id="25cd7-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="25cd7-108">Sviluppo di API con priorità al contratto usando i buffer del protocollo per impostazione predefinita e implementazioni indipendenti dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="25cd7-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="25cd7-109">Strumenti disponibili per molte linguaggi consentono di generare client e server fortemente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="25cd7-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="25cd7-110">Supporto per chiamate client, server e di streaming bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="25cd7-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="25cd7-111">Utilizzo di rete ridotto grazie alla serializzazione binaria Protobuf.</span><span class="sxs-lookup"><span data-stu-id="25cd7-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="25cd7-112">Questi vantaggi rendono gRPC ideale per:</span><span class="sxs-lookup"><span data-stu-id="25cd7-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="25cd7-113">Microservizi leggeri in cui l'efficienza è fondamentale.</span><span class="sxs-lookup"><span data-stu-id="25cd7-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="25cd7-114">Sistemi poliglotti che richiedono l'uso di più linguaggi per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="25cd7-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="25cd7-115">Servizi in tempo reale da punto a punto che devono gestire richieste o risposte di streaming.</span><span class="sxs-lookup"><span data-stu-id="25cd7-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="25cd7-116">Supporto degli strumenti C# per i file. proto</span><span class="sxs-lookup"><span data-stu-id="25cd7-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="25cd7-117">gRPC usa un approccio basato sul contratto per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="25cd7-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="25cd7-118">I servizi e i messaggi vengono definiti nei \* \*file. proto\* :</span><span class="sxs-lookup"><span data-stu-id="25cd7-118">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="25cd7-119">I tipi .NET per servizi, client e messaggi vengono generati automaticamente includendo \* \*file con estensione proto\* in un progetto:</span><span class="sxs-lookup"><span data-stu-id="25cd7-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="25cd7-120">Aggiungere un riferimento al pacchetto [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="25cd7-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="25cd7-121">Aggiungere \* \*i file. proto\* al `<Protobuf>` gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="25cd7-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="25cd7-122">Per ulteriori informazioni sul supporto per gli strumenti gRPC, <xref:grpc/basics>vedere.</span><span class="sxs-lookup"><span data-stu-id="25cd7-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="25cd7-123">Servizi gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25cd7-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="25cd7-124">i servizi gRPC possono essere ospitati in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25cd7-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="25cd7-125">I servizi hanno un'integrazione completa con le funzionalità DI ASP.NET Core più diffuse, ad esempio registrazione, inserimento DI dipendenze, autenticazione e autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="25cd7-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="25cd7-126">Il modello di progetto di servizio gRPC fornisce un servizio Starter:</span><span class="sxs-lookup"><span data-stu-id="25cd7-126">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="25cd7-127">`GreeterService`eredita dal `GreeterBase` tipo, generato dal `Greeter` servizio nel file \* \*. proto\* .</span><span class="sxs-lookup"><span data-stu-id="25cd7-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="25cd7-128">Il servizio è reso accessibile ai client in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="25cd7-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="25cd7-129">Per ulteriori informazioni sui servizi gRPC in ASP.NET Core, vedere <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="25cd7-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="25cd7-130">Chiamare i servizi gRPC con un client .NET</span><span class="sxs-lookup"><span data-stu-id="25cd7-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="25cd7-131">i client gRPC sono tipi di client concreti [generati \* \*da file. proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="25cd7-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="25cd7-132">Il client gRPC concreto dispone di metodi che vengono convertiti nel servizio gRPC nel file \* \*. proto\* .</span><span class="sxs-lookup"><span data-stu-id="25cd7-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="25cd7-133">Un client gRPC viene creato usando un canale che rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="25cd7-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="25cd7-134">Un canale può essere creato usando `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="25cd7-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="25cd7-135">Per ulteriori informazioni sulla creazione di client e sulla chiamata di diversi metodi di <xref:grpc/client>servizio, vedere.</span><span class="sxs-lookup"><span data-stu-id="25cd7-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="25cd7-136">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="25cd7-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
