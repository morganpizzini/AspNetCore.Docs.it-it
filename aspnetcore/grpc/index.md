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
# <a name="introduction-to-grpc-on-net-core"></a><span data-ttu-id="067ce-103">Introduzione a gRPC in .NET CoreIntroduction to gRPC on .NET Core</span><span class="sxs-lookup"><span data-stu-id="067ce-103">Introduction to gRPC on .NET Core</span></span>

<span data-ttu-id="067ce-104">Di [John Luo](https://github.com/juntaoluo) e [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="067ce-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="067ce-105">[gRPC](https://grpc.io/docs/guides/) è un framework RPC indipendente dal linguaggio a elevate prestazioni.</span><span class="sxs-lookup"><span data-stu-id="067ce-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="067ce-106">I principali vantaggi di gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="067ce-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="067ce-107">Framework RPC moderno, ad alte prestazioni e leggero.</span><span class="sxs-lookup"><span data-stu-id="067ce-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="067ce-108">Sviluppo di API con priorità al contratto usando i buffer del protocollo per impostazione predefinita e implementazioni indipendenti dal linguaggio.</span><span class="sxs-lookup"><span data-stu-id="067ce-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="067ce-109">Strumenti disponibili per molte linguaggi consentono di generare client e server fortemente tipizzati.</span><span class="sxs-lookup"><span data-stu-id="067ce-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="067ce-110">Supporto per chiamate client, server e di streaming bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="067ce-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="067ce-111">Utilizzo di rete ridotto grazie alla serializzazione binaria Protobuf.</span><span class="sxs-lookup"><span data-stu-id="067ce-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="067ce-112">Questi vantaggi rendono gRPC ideale per:</span><span class="sxs-lookup"><span data-stu-id="067ce-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="067ce-113">Microservizi leggeri in cui l'efficienza è fondamentale.</span><span class="sxs-lookup"><span data-stu-id="067ce-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="067ce-114">Sistemi poliglotti che richiedono l'uso di più linguaggi per lo sviluppo.</span><span class="sxs-lookup"><span data-stu-id="067ce-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="067ce-115">Servizi in tempo reale da punto a punto che devono gestire richieste o risposte di streaming.</span><span class="sxs-lookup"><span data-stu-id="067ce-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="067ce-116">Supporto degli strumenti di C</span><span class="sxs-lookup"><span data-stu-id="067ce-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="067ce-117">gRPC utilizza un approccio contract-first allo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="067ce-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="067ce-118">I servizi e i messaggi sono definiti in \* \*file .proto:\*</span><span class="sxs-lookup"><span data-stu-id="067ce-118">Services and messages are defined in *\*.proto* files:</span></span>

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

<span data-ttu-id="067ce-119">I tipi .NET per servizi, client \* \*\* e messaggi vengono generati automaticamente includendo i file proto in un progetto:</span><span class="sxs-lookup"><span data-stu-id="067ce-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="067ce-120">Aggiungere un riferimento al pacchetto [Grpc.Tools.Add](https://www.nuget.org/packages/Grpc.Tools/) a package reference to Grpc.Tools package.</span><span class="sxs-lookup"><span data-stu-id="067ce-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="067ce-121">Aggiungere \* \*\* file con estensione `<Protobuf>` proto al gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="067ce-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="067ce-122">Per ulteriori informazioni sul supporto degli <xref:grpc/basics>strumenti gRPC, vedere .</span><span class="sxs-lookup"><span data-stu-id="067ce-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="067ce-123">Servizi gRPC su ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="067ce-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="067ce-124">I servizi gRPC possono essere ospitati su ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="067ce-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="067ce-125">I servizi hanno una piena integrazione con le funzionalità di ASP.NET Core, come la registrazione, l'inserimento delle dipendenze (DI), l'autenticazione e l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="067ce-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="067ce-126">Il modello di progetto di servizio gRPC fornisce un servizio di avvio:The gRPC service project template provides a starter service:</span><span class="sxs-lookup"><span data-stu-id="067ce-126">The gRPC service project template provides a starter service:</span></span>

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

<span data-ttu-id="067ce-127">`GreeterService`eredita dal `GreeterBase` tipo, generato dal `Greeter` servizio nel file \* \*proto.\*</span><span class="sxs-lookup"><span data-stu-id="067ce-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="067ce-128">Il servizio è reso accessibile ai clienti in *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="067ce-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="067ce-129">Per ulteriori informazioni sui servizi gRPC <xref:grpc/aspnetcore>su ASP.NET Core, vedere .</span><span class="sxs-lookup"><span data-stu-id="067ce-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="067ce-130">Chiamare servizi gRPC con un client .NET</span><span class="sxs-lookup"><span data-stu-id="067ce-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="067ce-131">I client gRPC sono tipi di client concreti [generati da \* \*\* file con estensione proto](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="067ce-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="067ce-132">Il client gRPC concreto dispone di metodi che si traducono nel servizio gRPC nel file \* \*proto.\*</span><span class="sxs-lookup"><span data-stu-id="067ce-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="067ce-133">Un client gRPC viene creato utilizzando un canale, che rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="067ce-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="067ce-134">Un canale può `GrpcChannel.ForAddress`essere creato utilizzando .</span><span class="sxs-lookup"><span data-stu-id="067ce-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="067ce-135">Per ulteriori informazioni sulla creazione di client <xref:grpc/client>e sulla chiamata di metodi di servizio diversi, vedere .</span><span class="sxs-lookup"><span data-stu-id="067ce-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="067ce-136">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="067ce-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
