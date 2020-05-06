---
title: Servizi gRPC con C#
author: juntaoluo
description: Informazioni sui concetti di base per la scrittura di servizi gRPC con C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774756"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="327d0-103">Servizi gRPC con C\#</span><span class="sxs-lookup"><span data-stu-id="327d0-103">gRPC services with C\#</span></span>

<span data-ttu-id="327d0-104">Questo documento illustra i concetti necessari per scrivere app [gRPC](https://grpc.io/docs/guides/) in C#.</span><span class="sxs-lookup"><span data-stu-id="327d0-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="327d0-105">Gli argomenti trattati in questo articolo si applicano alle app gRPC basate su [C-core](https://grpc.io/blog/grpc-stacks)e su ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="327d0-106">file proto</span><span class="sxs-lookup"><span data-stu-id="327d0-106">proto file</span></span>

<span data-ttu-id="327d0-107">gRPC usa un approccio basato sul contratto per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="327d0-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="327d0-108">Per impostazione predefinita, i buffer di protocollo (protobuf) vengono usati come IDL (Interface Design Language).</span><span class="sxs-lookup"><span data-stu-id="327d0-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="327d0-109">Il file con \* \*estensione proto\* contiene:</span><span class="sxs-lookup"><span data-stu-id="327d0-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="327d0-110">Definizione del servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="327d0-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="327d0-111">Messaggi inviati tra client e server.</span><span class="sxs-lookup"><span data-stu-id="327d0-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="327d0-112">Per ulteriori informazioni sulla sintassi dei file protobuf, vedere la [documentazione ufficiale (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="327d0-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="327d0-113">Si consideri ad esempio il file *greet. proto* usato in [Introduzione al servizio gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="327d0-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="327d0-114">Definisce un `Greeter` servizio.</span><span class="sxs-lookup"><span data-stu-id="327d0-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="327d0-115">Il `Greeter` servizio definisce una `SayHello` chiamata.</span><span class="sxs-lookup"><span data-stu-id="327d0-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="327d0-116">`SayHello`Invia un `HelloRequest` messaggio e riceve un `HelloReply` messaggio:</span><span class="sxs-lookup"><span data-stu-id="327d0-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="327d0-117">Aggiungere un file. proto a un'app\# C</span><span class="sxs-lookup"><span data-stu-id="327d0-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="327d0-118">Il \* \*file con estensione proto\* è incluso in un progetto aggiungendolo al gruppo `<Protobuf>` di elementi:</span><span class="sxs-lookup"><span data-stu-id="327d0-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="327d0-119">Supporto degli strumenti C# per i file. proto</span><span class="sxs-lookup"><span data-stu-id="327d0-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="327d0-120">Il pacchetto di strumenti [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) è necessario per generare gli asset C# da \* \*file. proto\* .</span><span class="sxs-lookup"><span data-stu-id="327d0-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="327d0-121">Asset generati (file):</span><span class="sxs-lookup"><span data-stu-id="327d0-121">The generated assets (files):</span></span>

* <span data-ttu-id="327d0-122">Vengono generati ogni volta che il progetto viene compilato in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="327d0-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="327d0-123">Non vengono aggiunti al progetto o archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="327d0-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="327d0-124">È un artefatto di compilazione contenuto nella directory *obj* .</span><span class="sxs-lookup"><span data-stu-id="327d0-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="327d0-125">Questo pacchetto è necessario per i progetti server e client.</span><span class="sxs-lookup"><span data-stu-id="327d0-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="327d0-126">Il `Grpc.AspNetCore` metapacchetto include un riferimento a `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="327d0-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="327d0-127">I progetti server possono `Grpc.AspNetCore` essere aggiunti tramite Gestione pacchetti in Visual Studio o aggiungendo un `<PackageReference>` al file di progetto:</span><span class="sxs-lookup"><span data-stu-id="327d0-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="327d0-128">I progetti client devono fare `Grpc.Tools` riferimento direttamente a insieme agli altri pacchetti necessari per usare il client gRPC.</span><span class="sxs-lookup"><span data-stu-id="327d0-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="327d0-129">Il pacchetto di strumenti non è necessario in fase di esecuzione, quindi la dipendenza `PrivateAssets="All"`è contrassegnata con:</span><span class="sxs-lookup"><span data-stu-id="327d0-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="327d0-130">Asset C# generati</span><span class="sxs-lookup"><span data-stu-id="327d0-130">Generated C# assets</span></span>

<span data-ttu-id="327d0-131">Il pacchetto di strumenti genera i tipi C# che rappresentano i messaggi definiti nei file con \* \*estensione proto\* inclusi.</span><span class="sxs-lookup"><span data-stu-id="327d0-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="327d0-132">Per gli asset lato server, viene generato un tipo di base del servizio abstract.</span><span class="sxs-lookup"><span data-stu-id="327d0-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="327d0-133">Il tipo di base contiene le definizioni di tutte le chiamate gRPC contenute nel file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="327d0-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="327d0-134">Creare un'implementazione del servizio concreta che deriva da questo tipo di base e implementa la logica per le chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="327d0-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="327d0-135">Per `greet.proto`, l'esempio descritto in precedenza, viene generato `GreeterBase` un tipo astratto che contiene `SayHello` un metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="327d0-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="327d0-136">Un'implementazione `GreeterService` concreta esegue l'override del metodo e implementa la logica di gestione della chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="327d0-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="327d0-137">Per gli asset lato client viene generato un tipo di client concreto.</span><span class="sxs-lookup"><span data-stu-id="327d0-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="327d0-138">Le chiamate gRPC nel file *. proto* vengono convertite in metodi sul tipo concreto, che può essere chiamato.</span><span class="sxs-lookup"><span data-stu-id="327d0-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="327d0-139">Per l' `greet.proto`esempio descritto in precedenza, viene generato un `GreeterClient` tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="327d0-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="327d0-140">Chiamare `GreeterClient.SayHelloAsync` per avviare una chiamata gRPC al server.</span><span class="sxs-lookup"><span data-stu-id="327d0-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="327d0-141">Per impostazione predefinita, gli `<Protobuf>` asset server e client vengono generati per ogni \* \*file con estensione proto\* incluso nel gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="327d0-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="327d0-142">Per assicurarsi che vengano generati solo gli asset server in un progetto server, `GrpcServices` l'attributo è impostato `Server`su.</span><span class="sxs-lookup"><span data-stu-id="327d0-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="327d0-143">Analogamente, l'attributo viene impostato `Client` su nei progetti client.</span><span class="sxs-lookup"><span data-stu-id="327d0-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="327d0-144">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="327d0-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
