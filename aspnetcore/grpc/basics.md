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
# <a name="grpc-services-with-c"></a><span data-ttu-id="49e33-103">Servizi gRPC con C\#</span><span class="sxs-lookup"><span data-stu-id="49e33-103">gRPC services with C\#</span></span>

<span data-ttu-id="49e33-104">In questo documento vengono descritti i concetti necessari per scrivere app [gRPC](https://grpc.io/docs/guides/) in C.</span><span class="sxs-lookup"><span data-stu-id="49e33-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="49e33-105">Gli argomenti trattati qui si applicano sia alle app gRPC basate su [C-core](https://grpc.io/blog/grpc-stacks)che a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="49e33-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="49e33-106">proto file</span><span class="sxs-lookup"><span data-stu-id="49e33-106">proto file</span></span>

<span data-ttu-id="49e33-107">gRPC utilizza un approccio contract-first allo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="49e33-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="49e33-108">I buffer di protocollo (protobuf) vengono utilizzati come IDL (Interface Design Language) per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="49e33-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="49e33-109">Il file \* \*proto\* contiene:</span><span class="sxs-lookup"><span data-stu-id="49e33-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="49e33-110">Definizione del servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="49e33-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="49e33-111">Messaggi inviati tra client e server.</span><span class="sxs-lookup"><span data-stu-id="49e33-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="49e33-112">Per ulteriori informazioni sulla sintassi dei file protobuf, vedere la [documentazione ufficiale (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="49e33-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="49e33-113">Si consideri, ad esempio, il file *greet.proto* utilizzato in [Introduzione al servizio gRPC](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="49e33-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="49e33-114">Definisce `Greeter` un servizio.</span><span class="sxs-lookup"><span data-stu-id="49e33-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="49e33-115">Il `Greeter` servizio definisce una `SayHello` chiamata.</span><span class="sxs-lookup"><span data-stu-id="49e33-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="49e33-116">`SayHello`invia `HelloRequest` un messaggio e `HelloReply` riceve un messaggio:</span><span class="sxs-lookup"><span data-stu-id="49e33-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="49e33-117">Aggiungere un file .proto\# a un'app C</span><span class="sxs-lookup"><span data-stu-id="49e33-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="49e33-118">Il \* \*\* file proto viene incluso in un `<Protobuf>` progetto aggiungendolo al gruppo di elementi:</span><span class="sxs-lookup"><span data-stu-id="49e33-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="49e33-119">Supporto degli strumenti di C</span><span class="sxs-lookup"><span data-stu-id="49e33-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="49e33-120">Il pacchetto di strumenti [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) è necessario per generare le risorse di C' dai \* \*\* file con estensione proto.</span><span class="sxs-lookup"><span data-stu-id="49e33-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="49e33-121">Le risorse generate (file):</span><span class="sxs-lookup"><span data-stu-id="49e33-121">The generated assets (files):</span></span>

* <span data-ttu-id="49e33-122">Vengono generati in base alle esigenze ogni volta che il progetto viene compilato.</span><span class="sxs-lookup"><span data-stu-id="49e33-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="49e33-123">Non vengono aggiunti al progetto o archiviati nel controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="49e33-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="49e33-124">Sono un elemento di compilazione contenuto nella directory *obj.*</span><span class="sxs-lookup"><span data-stu-id="49e33-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="49e33-125">Questo pacchetto è richiesto sia dai progetti server che da quelli client.</span><span class="sxs-lookup"><span data-stu-id="49e33-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="49e33-126">Il `Grpc.AspNetCore` metapacchetto include `Grpc.Tools`un riferimento a .</span><span class="sxs-lookup"><span data-stu-id="49e33-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="49e33-127">I progetti `Grpc.AspNetCore` server possono aggiungere usando Gestione pacchetti `<PackageReference>` in Visual Studio o aggiungendo un oggetto al file di progetto:Server projects can add using the Package Manager in Visual Studio or by adding a to the project file:</span><span class="sxs-lookup"><span data-stu-id="49e33-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="49e33-128">I progetti client `Grpc.Tools` devono fare riferimento direttamente insieme agli altri pacchetti necessari per utilizzare il client gRPC.</span><span class="sxs-lookup"><span data-stu-id="49e33-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="49e33-129">Il pacchetto di strumenti non è necessario in fase `PrivateAssets="All"`di esecuzione, pertanto la dipendenza è contrassegnata con :</span><span class="sxs-lookup"><span data-stu-id="49e33-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="49e33-130">Risorse di C 'A'generated</span><span class="sxs-lookup"><span data-stu-id="49e33-130">Generated C# assets</span></span>

<span data-ttu-id="49e33-131">Il pacchetto di strumenti genera i tipi di C , che rappresentano i messaggi definiti nei file \* \*con estensione proto\* inclusi.</span><span class="sxs-lookup"><span data-stu-id="49e33-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="49e33-132">Per gli asset lato server, viene generato un tipo di base di servizio astratto.</span><span class="sxs-lookup"><span data-stu-id="49e33-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="49e33-133">Il tipo di base contiene le definizioni di tutte le chiamate gRPC contenute nel file *proto.*</span><span class="sxs-lookup"><span data-stu-id="49e33-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="49e33-134">Creare un'implementazione del servizio concreta che deriva da questo tipo di base e implementa la logica per le chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="49e33-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="49e33-135">Per `greet.proto`l'esempio descritto in `GreeterBase` precedenza, viene `SayHello` generato un tipo astratto contenente un metodo virtuale.</span><span class="sxs-lookup"><span data-stu-id="49e33-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="49e33-136">Un'implementazione `GreeterService` concreta esegue l'override del metodo e implementa la logica che gestisce la chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="49e33-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="49e33-137">Per le risorse lato client, viene generato un tipo di client concreto.</span><span class="sxs-lookup"><span data-stu-id="49e33-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="49e33-138">Le chiamate gRPC nel file *proto* vengono convertite in metodi sul tipo concreto, che possono essere chiamati.</span><span class="sxs-lookup"><span data-stu-id="49e33-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="49e33-139">Per `greet.proto`l'esempio descritto in `GreeterClient` precedenza, viene generato un tipo concreto.</span><span class="sxs-lookup"><span data-stu-id="49e33-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="49e33-140">Chiamata `GreeterClient.SayHelloAsync` per avviare una chiamata gRPC al server.</span><span class="sxs-lookup"><span data-stu-id="49e33-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="49e33-141">Per impostazione predefinita, le risorse server e client `<Protobuf>` vengono generate per ogni \* \*\* file proto incluso nel gruppo di elementi.</span><span class="sxs-lookup"><span data-stu-id="49e33-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="49e33-142">Per garantire che in un progetto server `GrpcServices` vengano generate `Server`solo le risorse del server, l'attributo è impostato su .</span><span class="sxs-lookup"><span data-stu-id="49e33-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="49e33-143">Analogamente, l'attributo è impostato su `Client` nei progetti client.</span><span class="sxs-lookup"><span data-stu-id="49e33-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="49e33-144">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="49e33-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
