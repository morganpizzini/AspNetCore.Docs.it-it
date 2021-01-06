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
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="785b9-103">Servizi e client gRPC Code-First con .NET</span><span class="sxs-lookup"><span data-stu-id="785b9-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="785b9-104">Di [James Newton-King](https://twitter.com/jamesnk) e [Marc ghiaial](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="785b9-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="785b9-105">GRPC Code-First usa i tipi .NET per definire contratti di servizio e di messaggio.</span><span class="sxs-lookup"><span data-stu-id="785b9-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="785b9-106">Code-First è una scelta ottimale quando un intero sistema USA .NET:</span><span class="sxs-lookup"><span data-stu-id="785b9-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="785b9-107">Il servizio .NET e i tipi di contratto dati possono essere condivisi tra il server .NET e i client.</span><span class="sxs-lookup"><span data-stu-id="785b9-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="785b9-108">Evita la necessità di definire contratti nei `.proto` file e nella generazione del codice.</span><span class="sxs-lookup"><span data-stu-id="785b9-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="785b9-109">Code-First non è consigliato nei sistemi poliglotti con più lingue.</span><span class="sxs-lookup"><span data-stu-id="785b9-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="785b9-110">Il servizio .NET e i tipi di contratto dati non possono essere usati con le piattaforme non-.NET.</span><span class="sxs-lookup"><span data-stu-id="785b9-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="785b9-111">Per chiamare un servizio gRPC scritto usando Code-First, altre piattaforme devono creare un `.proto` contratto che corrisponda al servizio.</span><span class="sxs-lookup"><span data-stu-id="785b9-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="785b9-112">protobuf-net. Grpc</span><span class="sxs-lookup"><span data-stu-id="785b9-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="785b9-113">Per informazioni su protobuf-net. Grpc, visitare il [protobuf-net. Sito Web Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) o creare un problema in [protobuf-net. Repository GitHub Grpc](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="785b9-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="785b9-114">[protobuf-net. Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) è un progetto della community e non è supportato da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="785b9-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="785b9-115">Viene aggiunto il supporto del primo codice a `Grpc.AspNetCore` e `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="785b9-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="785b9-116">USA i tipi .NET annotati con gli attributi per definire i servizi e i messaggi di gRPC di un'app.</span><span class="sxs-lookup"><span data-stu-id="785b9-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="785b9-117">Il primo passaggio per la creazione di un servizio gRPC Code-First consiste nel definire il contratto di codice:</span><span class="sxs-lookup"><span data-stu-id="785b9-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="785b9-118">Creare un nuovo progetto che verrà condiviso dal server e dal client.</span><span class="sxs-lookup"><span data-stu-id="785b9-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="785b9-119">Aggiungere un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Riferimento al pacchetto Grpc.</span><span class="sxs-lookup"><span data-stu-id="785b9-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="785b9-120">Creare i tipi di contratto di servizio e dati.</span><span class="sxs-lookup"><span data-stu-id="785b9-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="785b9-121">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="785b9-121">The preceding code:</span></span>

* <span data-ttu-id="785b9-122">Definisce `HelloRequest` `HelloReply` i messaggi e.</span><span class="sxs-lookup"><span data-stu-id="785b9-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="785b9-123">Definisce l' `IGreeterService` interfaccia del contratto con il `SayHelloAsync` Metodo unario gRPC.</span><span class="sxs-lookup"><span data-stu-id="785b9-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="785b9-124">Il contratto di servizio viene implementato nel server e viene chiamato dal client.</span><span class="sxs-lookup"><span data-stu-id="785b9-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="785b9-125">I metodi definiti nelle interfacce del servizio devono corrispondere a determinate firme a seconda che si tratti di un flusso unario, di streaming del server, di streaming client o bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="785b9-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="785b9-126">Per ulteriori informazioni sulla definizione dei contratti di servizio, vedere [protobuf-net. Documentazione introduttiva di Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="785b9-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="785b9-127">Creare un servizio gRPC Code-First</span><span class="sxs-lookup"><span data-stu-id="785b9-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="785b9-128">Per aggiungere il servizio gRPC Code-First a un'app ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="785b9-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="785b9-129">Aggiungere un [protobuf-net. Riferimento al pacchetto Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="785b9-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="785b9-130">Aggiungere un riferimento al progetto del contratto di codice condiviso.</span><span class="sxs-lookup"><span data-stu-id="785b9-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="785b9-131">Creare un nuovo `GreeterService.cs` file e implementare l' `IGreeterService` interfaccia del servizio:</span><span class="sxs-lookup"><span data-stu-id="785b9-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="785b9-132">Aggiornare il `Startup.cs` file:</span><span class="sxs-lookup"><span data-stu-id="785b9-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="785b9-133">Nel codice precedente:</span><span class="sxs-lookup"><span data-stu-id="785b9-133">In the preceding code:</span></span>

* <span data-ttu-id="785b9-134">`AddCodeFirstGrpc` registra i servizi che consentono la prima codifica.</span><span class="sxs-lookup"><span data-stu-id="785b9-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="785b9-135">`MapGrpcService<GreeterService>` aggiunge l'endpoint del servizio Code-First.</span><span class="sxs-lookup"><span data-stu-id="785b9-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="785b9-136">i servizi gRPC implementati con Code-First e `.proto` i file possono coesistere nella stessa app.</span><span class="sxs-lookup"><span data-stu-id="785b9-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="785b9-137">Tutti i servizi gRPC usano la [configurazione del servizio gRPC](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="785b9-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="785b9-138">Creare un client gRPC Code-First</span><span class="sxs-lookup"><span data-stu-id="785b9-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="785b9-139">Un client gRPC Code-First usa il contratto di servizio per chiamare i servizi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="785b9-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="785b9-140">Per chiamare un servizio gRPC usando un client Code-First:</span><span class="sxs-lookup"><span data-stu-id="785b9-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="785b9-141">Aggiungere un [protobuf-net. ](https://www.nuget.org/packages/protobuf-net.Grpc) Riferimento al pacchetto Grpc.</span><span class="sxs-lookup"><span data-stu-id="785b9-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="785b9-142">Aggiungere un riferimento al progetto del contratto di codice condiviso.</span><span class="sxs-lookup"><span data-stu-id="785b9-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="785b9-143">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="785b9-143">The preceding code:</span></span>

* <span data-ttu-id="785b9-144">Crea un canale gRPC.</span><span class="sxs-lookup"><span data-stu-id="785b9-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="785b9-145">Crea un client del primo codice dal canale con il `CreateGrpcService<IGreeterService>` metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="785b9-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="785b9-146">Chiama il servizio gRPC con `SayHelloAsync` .</span><span class="sxs-lookup"><span data-stu-id="785b9-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="785b9-147">Un client gRPC Code-First viene creato da un canale.</span><span class="sxs-lookup"><span data-stu-id="785b9-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="785b9-148">Proprio come un normale client, un client Code-First usa la relativa [configurazione del canale](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="785b9-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="785b9-149">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="785b9-149">Additional resources</span></span>

* [<span data-ttu-id="785b9-150">protobuf-net. Sito Web Grpc</span><span class="sxs-lookup"><span data-stu-id="785b9-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="785b9-151">protobuf-net. Repository GitHub Grpc</span><span class="sxs-lookup"><span data-stu-id="785b9-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
