---
title: Testare i servizi con gli strumenti gRPC
author: jamesnk
description: Informazioni su come testare i servizi con gli strumenti gRPC. gRPCurl uno strumento da riga di comando per l'interazione con i servizi di gRPC. gRPCui è un'interfaccia utente Web interattiva.
monikerRange: '>= aspnetcore-3.0'
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
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594418"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="bc15d-105">Testare i servizi con gli strumenti gRPC</span><span class="sxs-lookup"><span data-stu-id="bc15d-105">Test services with gRPC tools</span></span>

<span data-ttu-id="bc15d-106">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="bc15d-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="bc15d-107">Gli strumenti sono disponibili per gRPC che consentono agli sviluppatori di testare i servizi senza creare app client.</span><span class="sxs-lookup"><span data-stu-id="bc15d-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="bc15d-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) è uno strumento da riga di comando che consente di interagire con i servizi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="bc15d-109">[gRPCui](https://github.com/fullstorydev/grpcui) aggiunge un'interfaccia utente Web interattiva per gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="bc15d-110">Questo articolo illustra come:</span><span class="sxs-lookup"><span data-stu-id="bc15d-110">This article discusses how to:</span></span>

* <span data-ttu-id="bc15d-111">Scaricare e installare gRPCurl e gRPCui.</span><span class="sxs-lookup"><span data-stu-id="bc15d-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="bc15d-112">Configurare la reflection gRPC con un'app gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc15d-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="bc15d-113">Individuare e testare i servizi gRPC con `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="bc15d-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="bc15d-114">Interagire con i servizi gRPC tramite un browser usando `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="bc15d-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="bc15d-115">Informazioni su gRPCurl</span><span class="sxs-lookup"><span data-stu-id="bc15d-115">About gRPCurl</span></span>

<span data-ttu-id="bc15d-116">gRPCurl è uno strumento da riga di comando creato dalla community gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="bc15d-117">Le funzionalità includono:</span><span class="sxs-lookup"><span data-stu-id="bc15d-117">Its features include:</span></span>

* <span data-ttu-id="bc15d-118">Chiamata dei servizi gRPC, inclusi i servizi di streaming.</span><span class="sxs-lookup"><span data-stu-id="bc15d-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="bc15d-119">Individuazione del servizio mediante [Reflection gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="bc15d-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="bc15d-120">Elenco e descrizione dei servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="bc15d-121">Funziona con server protetti (TLS) e non sicuri (testo normale).</span><span class="sxs-lookup"><span data-stu-id="bc15d-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="bc15d-122">Per informazioni sul download e l'installazione di `grpcurl` , vedere la [Home page di GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="bc15d-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="bc15d-123">Configurare la reflection gRPC</span><span class="sxs-lookup"><span data-stu-id="bc15d-123">Setup gRPC reflection</span></span>

<span data-ttu-id="bc15d-124">`grpcurl` deve essere a conoscenza del contratto protobuf dei servizi prima che possa chiamarli.</span><span class="sxs-lookup"><span data-stu-id="bc15d-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="bc15d-125">A questo scopo è possibile procedere in due modi:</span><span class="sxs-lookup"><span data-stu-id="bc15d-125">There are two ways to do this:</span></span>

* <span data-ttu-id="bc15d-126">Utilizzare la reflection gRPC per individuare i contratti di servizio.</span><span class="sxs-lookup"><span data-stu-id="bc15d-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="bc15d-127">Specificare i file con *estensione proto* negli argomenti della riga di comando.</span><span class="sxs-lookup"><span data-stu-id="bc15d-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="bc15d-128">È più facile usare gRPCurl con la reflection gRPC e l'individuazione dei servizi.</span><span class="sxs-lookup"><span data-stu-id="bc15d-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="bc15d-129">gRPC ASP.NET Core dispone del supporto incorporato per la reflection gRPC con il pacchetto [gRPC. AspNetCore. Server. Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) .</span><span class="sxs-lookup"><span data-stu-id="bc15d-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="bc15d-130">Per configurare la reflection in un'app:</span><span class="sxs-lookup"><span data-stu-id="bc15d-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="bc15d-131">Aggiungere un `Grpc.AspNetCore.Server.Reflection` riferimento al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="bc15d-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="bc15d-132">Registrare la reflection in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="bc15d-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="bc15d-133">`AddGrpcReflection` per registrare servizi che abilitano la reflection.</span><span class="sxs-lookup"><span data-stu-id="bc15d-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="bc15d-134">`MapGrpcReflectionService` per aggiungere l'endpoint del servizio Reflection.</span><span class="sxs-lookup"><span data-stu-id="bc15d-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="bc15d-135">Utilizzare `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="bc15d-135">Use `grpcurl`</span></span>

<span data-ttu-id="bc15d-136">Nell' `-help` argomento vengono illustrate le `grpcurl` Opzioni della riga di comando:</span><span class="sxs-lookup"><span data-stu-id="bc15d-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="bc15d-137">Individuare i servizi</span><span class="sxs-lookup"><span data-stu-id="bc15d-137">Discover services</span></span>

<span data-ttu-id="bc15d-138">Usare il `describe` verbo per visualizzare i servizi definiti dal server:</span><span class="sxs-lookup"><span data-stu-id="bc15d-138">Use the `describe` verb to view the services defined by the server:</span></span>

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

<span data-ttu-id="bc15d-139">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="bc15d-139">The preceding example:</span></span>

* <span data-ttu-id="bc15d-140">Esegue `describe` il verbo nel server `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="bc15d-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="bc15d-141">Stampa i servizi e i metodi restituiti dalla reflection gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="bc15d-142">`Greeter` è un servizio implementato dall'app.</span><span class="sxs-lookup"><span data-stu-id="bc15d-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="bc15d-143">`ServerReflection` servizio aggiunto dal `Grpc.AspNetCore.Server.Reflection` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="bc15d-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="bc15d-144">Combinare `describe` con un servizio, un metodo o un nome di messaggio per visualizzarne i dettagli:</span><span class="sxs-lookup"><span data-stu-id="bc15d-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="bc15d-145">Chiamare i servizi gRPC</span><span class="sxs-lookup"><span data-stu-id="bc15d-145">Call gRPC services</span></span>

<span data-ttu-id="bc15d-146">Chiamare un servizio gRPC specificando un nome di servizio e metodo, insieme a un argomento JSON che rappresenta il messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="bc15d-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="bc15d-147">Il codice JSON viene convertito in protobuf e inviato al servizio.</span><span class="sxs-lookup"><span data-stu-id="bc15d-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="bc15d-148">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="bc15d-148">The preceding example:</span></span>

* <span data-ttu-id="bc15d-149">`-d` l'argomento specifica un messaggio di richiesta con JSON.</span><span class="sxs-lookup"><span data-stu-id="bc15d-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="bc15d-150">Questo argomento deve precedere l'indirizzo del server e il nome del metodo.</span><span class="sxs-lookup"><span data-stu-id="bc15d-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="bc15d-151">Chiama il `SayHello` metodo sul `greeter.Greeter` servizio.</span><span class="sxs-lookup"><span data-stu-id="bc15d-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="bc15d-152">Stampa il messaggio di risposta come JSON.</span><span class="sxs-lookup"><span data-stu-id="bc15d-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="bc15d-153">Informazioni su gRPCui</span><span class="sxs-lookup"><span data-stu-id="bc15d-153">About gRPCui</span></span>

<span data-ttu-id="bc15d-154">gRPCui è un'interfaccia utente Web interattiva per gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="bc15d-155">Si basa su gRPCurl e offre un'interfaccia utente grafica per l'individuazione e il testing dei servizi gRPC, in modo analogo a strumenti HTTP come il postazione.</span><span class="sxs-lookup"><span data-stu-id="bc15d-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="bc15d-156">Per informazioni sul download e l'installazione di `grpcui` , vedere la [Home page di GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="bc15d-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="bc15d-157">Uso di `grpcui`</span><span class="sxs-lookup"><span data-stu-id="bc15d-157">Using `grpcui`</span></span>

<span data-ttu-id="bc15d-158">Eseguire `grpcui` con l'indirizzo del server per interagire con come argomento.</span><span class="sxs-lookup"><span data-stu-id="bc15d-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="bc15d-159">Lo strumento avvierà una finestra del browser con l'interfaccia utente Web interattiva.</span><span class="sxs-lookup"><span data-stu-id="bc15d-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="bc15d-160">i servizi gRPC vengono individuati automaticamente usando la reflection gRPC.</span><span class="sxs-lookup"><span data-stu-id="bc15d-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interfaccia utente Web di gRPCuiri](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="bc15d-162">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="bc15d-162">Additional resources</span></span>

* [<span data-ttu-id="bc15d-163">Home page di GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="bc15d-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="bc15d-164">Home page di GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="bc15d-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="bc15d-165">Grpc. AspNetCore. Server. Reflection</span><span class="sxs-lookup"><span data-stu-id="bc15d-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
