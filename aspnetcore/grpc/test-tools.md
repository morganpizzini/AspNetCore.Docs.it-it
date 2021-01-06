---
title: Testare i servizi gRPC con gRPCurl in ASP.NET Core
author: jamesnk
description: Informazioni su come testare i servizi con gli strumenti gRPC. gRPCurl uno strumento da riga di comando per l'interazione con i servizi di gRPC. gRPCui è un'interfaccia utente Web interattiva.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058792"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="1c4d0-105">Testare i servizi gRPC con gRPCurl in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1c4d0-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="1c4d0-106">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1c4d0-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="1c4d0-107">Gli strumenti sono disponibili per gRPC che consentono agli sviluppatori di testare i servizi senza creare app client:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="1c4d0-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) è uno strumento da riga di comando che consente di interagire con i servizi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="1c4d0-109">[gRPCui](https://github.com/fullstorydev/grpcui) si basa su gRPCurl e aggiunge un'interfaccia utente Web interattiva per gRPC, in modo analogo a strumenti come subspettatore e interfaccia utente di spavalderia.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="1c4d0-110">Questo articolo illustra come:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-110">This article discusses how to:</span></span>

* <span data-ttu-id="1c4d0-111">Scaricare e installare gRPCurl e gRPCui.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="1c4d0-112">Configurare la reflection gRPC con un'app gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="1c4d0-113">Individuare e testare i servizi gRPC con `grpcurl` .</span><span class="sxs-lookup"><span data-stu-id="1c4d0-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="1c4d0-114">Interagire con i servizi gRPC tramite un browser usando `grpcui` .</span><span class="sxs-lookup"><span data-stu-id="1c4d0-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="1c4d0-115">Informazioni su gRPCurl</span><span class="sxs-lookup"><span data-stu-id="1c4d0-115">About gRPCurl</span></span>

<span data-ttu-id="1c4d0-116">gRPCurl è uno strumento da riga di comando creato dalla community gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="1c4d0-117">Le funzionalità includono:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-117">Its features include:</span></span>

* <span data-ttu-id="1c4d0-118">Chiamata dei servizi gRPC, inclusi i servizi di streaming.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="1c4d0-119">Individuazione del servizio mediante [Reflection gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span><span class="sxs-lookup"><span data-stu-id="1c4d0-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="1c4d0-120">Elenco e descrizione dei servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="1c4d0-121">Funziona con server protetti (TLS) e non sicuri (testo normale).</span><span class="sxs-lookup"><span data-stu-id="1c4d0-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="1c4d0-122">Per informazioni sul download e l'installazione di `grpcurl` , vedere la [Home page di GitHub gRPCurl](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="1c4d0-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![riga di comando di gRPCurl](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="1c4d0-124">Configurare la reflection gRPC</span><span class="sxs-lookup"><span data-stu-id="1c4d0-124">Set up gRPC reflection</span></span>

<span data-ttu-id="1c4d0-125">`grpcurl` deve essere a conoscenza del contratto protobuf dei servizi prima che possa chiamarli.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="1c4d0-126">A questo scopo è possibile procedere in due modi:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-126">There are two ways to do this:</span></span>

* <span data-ttu-id="1c4d0-127">Configurare la [Reflection gRPC](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) nel server.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="1c4d0-128">gRPCurl individua automaticamente i contratti di servizio.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="1c4d0-129">Specificare `.proto` i file negli argomenti della riga di comando per gRPCurl.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="1c4d0-130">L'uso di gRPCurl con la reflection gRPC è più semplice.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="1c4d0-131">gRPC Reflection aggiunge un nuovo servizio gRPC all'app che i client possono chiamare per individuare i servizi.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="1c4d0-132">gRPC ASP.NET Core dispone del supporto incorporato per la reflection gRPC con il [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) pacchetto.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="1c4d0-133">Per configurare la reflection in un'app:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="1c4d0-134">Aggiungere un `Grpc.AspNetCore.Server.Reflection` riferimento al pacchetto.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="1c4d0-135">Registra Reflection in `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="1c4d0-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="1c4d0-136">`AddGrpcReflection` per registrare servizi che abilitano la reflection.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="1c4d0-137">`MapGrpcReflectionService` per aggiungere un endpoint del servizio Reflection.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="1c4d0-138">Quando la reflection gRPC è impostata:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="1c4d0-139">Un servizio di Reflection gRPC viene aggiunto all'app Server.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="1c4d0-140">Le app client che supportano la reflection gRPC possono chiamare il servizio reflection per individuare i servizi ospitati dal server.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="1c4d0-141">i servizi gRPC vengono ancora chiamati dal client.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="1c4d0-142">La reflection Abilita solo l'individuazione del servizio e non ignora la sicurezza lato server.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="1c4d0-143">Per gli endpoint protetti dall' [autenticazione e dall'autorizzazione](xref:grpc/authn-and-authz) è necessario che il chiamante passi le credenziali affinché l'endpoint venga chiamato correttamente.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="1c4d0-144">Utilizzare `grpcurl`.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-144">Use `grpcurl`</span></span>

<span data-ttu-id="1c4d0-145">Nell' `-help` argomento vengono illustrate le `grpcurl` Opzioni della riga di comando:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="1c4d0-146">Individuare i servizi</span><span class="sxs-lookup"><span data-stu-id="1c4d0-146">Discover services</span></span>

<span data-ttu-id="1c4d0-147">Usare il `describe` verbo per visualizzare i servizi definiti dal server:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
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

<span data-ttu-id="1c4d0-148">L'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-148">The preceding example:</span></span>

* <span data-ttu-id="1c4d0-149">Esegue il `describe` verbo nel server `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="1c4d0-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="1c4d0-150">Stampa i servizi e i metodi restituiti dalla reflection gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="1c4d0-151">`Greeter` è un servizio implementato dall'app.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="1c4d0-152">`ServerReflection` servizio aggiunto dal `Grpc.AspNetCore.Server.Reflection` pacchetto.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="1c4d0-153">Combinare `describe` con un servizio, un metodo o un nome di messaggio per visualizzarne i dettagli:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="1c4d0-154">Chiamare i servizi gRPC</span><span class="sxs-lookup"><span data-stu-id="1c4d0-154">Call gRPC services</span></span>

<span data-ttu-id="1c4d0-155">Chiamare un servizio gRPC specificando un nome di servizio e di metodo insieme a un argomento JSON che rappresenta il messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="1c4d0-156">Il codice JSON viene convertito in protobuf e inviato al servizio.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="1c4d0-157">Nell'esempio precedente:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-157">In the preceding example:</span></span>

* <span data-ttu-id="1c4d0-158">L' `-d` argomento specifica un messaggio di richiesta con JSON.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="1c4d0-159">Questo argomento deve precedere l'indirizzo del server e il nome del metodo.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="1c4d0-160">Chiama il `SayHello` metodo sul `greeter.Greeter` servizio.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="1c4d0-161">Stampa il messaggio di risposta come JSON.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="1c4d0-162">Informazioni su gRPCui</span><span class="sxs-lookup"><span data-stu-id="1c4d0-162">About gRPCui</span></span>

<span data-ttu-id="1c4d0-163">gRPCui è un'interfaccia utente Web interattiva per gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="1c4d0-164">Si basa su gRPCurl e offre un'interfaccia utente grafica per l'individuazione e il testing dei servizi gRPC, in modo analogo agli strumenti HTTP come il postazione o l'interfaccia utente di spavalderia.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="1c4d0-165">Per informazioni sul download e l'installazione di `grpcui` , vedere la [Home page di GitHub gRPCui](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="1c4d0-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="1c4d0-166">Uso di `grpcui`</span><span class="sxs-lookup"><span data-stu-id="1c4d0-166">Using `grpcui`</span></span>

<span data-ttu-id="1c4d0-167">Eseguire `grpcui` con l'indirizzo del server per interagire con come argomento:</span><span class="sxs-lookup"><span data-stu-id="1c4d0-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="1c4d0-168">Lo strumento avvia una finestra del browser con l'interfaccia utente Web interattiva.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="1c4d0-169">i servizi gRPC vengono individuati automaticamente usando la reflection gRPC.</span><span class="sxs-lookup"><span data-stu-id="1c4d0-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![interfaccia utente Web di gRPCuiri](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="1c4d0-171">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="1c4d0-171">Additional resources</span></span>

* [<span data-ttu-id="1c4d0-172">Home page di GitHub gRPCurl</span><span class="sxs-lookup"><span data-stu-id="1c4d0-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="1c4d0-173">Home page di GitHub gRPCui</span><span class="sxs-lookup"><span data-stu-id="1c4d0-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
