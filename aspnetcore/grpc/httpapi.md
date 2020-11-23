---
title: Create API Web JSON da gRPC
author: jamesnk
description: Informazioni su come creare API HTTP JSON per i servizi gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 64d18114e2fe9ee10edb902a98a281c3cd9f3393
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417578"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="75e0b-103">Create API Web JSON da gRPC</span><span class="sxs-lookup"><span data-stu-id="75e0b-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="75e0b-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="75e0b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="75e0b-105">l'API HTTP gRPC è un progetto sperimentale, non un prodotto di cui è stato eseguito il commit.</span><span class="sxs-lookup"><span data-stu-id="75e0b-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="75e0b-106">Si desidera:</span><span class="sxs-lookup"><span data-stu-id="75e0b-106">We want to:</span></span>
>
> * <span data-ttu-id="75e0b-107">Verificare che l'approccio alla creazione di API Web JSON per i servizi gRPC funzioni.</span><span class="sxs-lookup"><span data-stu-id="75e0b-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="75e0b-108">Ottenere commenti e suggerimenti su se questo approccio è utile per gli sviluppatori .NET.</span><span class="sxs-lookup"><span data-stu-id="75e0b-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="75e0b-109">[Invia commenti e suggerimenti](https://github.com/grpc/grpc-dotnet/issues/167) per assicurarsi di creare qualcosa che gli sviluppatori desiderano e siano produttivi.</span><span class="sxs-lookup"><span data-stu-id="75e0b-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="75e0b-110">gRPC è un modo moderno per comunicare tra le app.</span><span class="sxs-lookup"><span data-stu-id="75e0b-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="75e0b-111">gRPC Usa HTTP/2, flusso, protobuf e contratti di messaggio per creare servizi in tempo reale ad alte prestazioni.</span><span class="sxs-lookup"><span data-stu-id="75e0b-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="75e0b-112">Una limitazione di gRPC non è la piattaforma che può essere usata da tutte le piattaforme.</span><span class="sxs-lookup"><span data-stu-id="75e0b-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="75e0b-113">I browser non supportano completamente HTTP/2, rendendo REST e JSON il modo principale per ottenere i dati nelle app del browser.</span><span class="sxs-lookup"><span data-stu-id="75e0b-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="75e0b-114">Anche con i vantaggi offerti da gRPC, REST e JSON hanno un posto importante nelle app moderne.</span><span class="sxs-lookup"><span data-stu-id="75e0b-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="75e0b-115">La compilazione di API Web gRPC \***e** _ JSON aggiunge un sovraccarico indesiderato per lo sviluppo di app.</span><span class="sxs-lookup"><span data-stu-id="75e0b-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="75e0b-116">Questo documento illustra come creare API Web JSON usando i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="75e0b-117">API HTTP gRPC</span><span class="sxs-lookup"><span data-stu-id="75e0b-117">gRPC HTTP API</span></span>

<span data-ttu-id="75e0b-118">l'API HTTP di gRPC è un'estensione sperimentale per ASP.NET Core che crea API JSON RESTful per i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="75e0b-119">Una volta configurata, l'API HTTP gRPC consente alle app di chiamare i servizi gRPC con concetti HTTP noti:</span><span class="sxs-lookup"><span data-stu-id="75e0b-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="75e0b-120">_ Verbi HTTP</span><span class="sxs-lookup"><span data-stu-id="75e0b-120">_ HTTP verbs</span></span>
* <span data-ttu-id="75e0b-121">Binding parametro URL</span><span class="sxs-lookup"><span data-stu-id="75e0b-121">URL parameter binding</span></span>
* <span data-ttu-id="75e0b-122">Richieste/risposte JSON</span><span class="sxs-lookup"><span data-stu-id="75e0b-122">JSON requests/responses</span></span>

<span data-ttu-id="75e0b-123">gRPC può comunque essere usato per chiamare i servizi.</span><span class="sxs-lookup"><span data-stu-id="75e0b-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="75e0b-124">Utilizzo</span><span class="sxs-lookup"><span data-stu-id="75e0b-124">Usage</span></span>

1. <span data-ttu-id="75e0b-125">Aggiungere un riferimento al pacchetto a [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="75e0b-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="75e0b-126">Registrare i servizi in *Startup.cs* con `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="75e0b-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="75e0b-127">Aggiungere i file [Google/API/HTTP. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) e [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) al progetto.</span><span class="sxs-lookup"><span data-stu-id="75e0b-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="75e0b-128">Annotare i metodi gRPC nei file *. proto* con binding e route http:</span><span class="sxs-lookup"><span data-stu-id="75e0b-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="75e0b-129">Il `SayHello` Metodo gRPC può ora essere richiamato come gRPC + protobuf e come API http:</span><span class="sxs-lookup"><span data-stu-id="75e0b-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="75e0b-130">Richiesta: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="75e0b-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="75e0b-131">Risposta: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="75e0b-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="75e0b-132">I log del server indicano che la chiamata HTTP viene eseguita da un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="75e0b-133">l'API HTTP gRPC esegue il mapping della richiesta HTTP in ingresso a un messaggio gRPC e quindi converte il messaggio di risposta in JSON.</span><span class="sxs-lookup"><span data-stu-id="75e0b-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="75e0b-134">Questo è un esempio di base.</span><span class="sxs-lookup"><span data-stu-id="75e0b-134">This is a basic example.</span></span> <span data-ttu-id="75e0b-135">Per altre opzioni di personalizzazione, vedere [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .</span><span class="sxs-lookup"><span data-stu-id="75e0b-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="75e0b-136">API gRPC HTTP vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="75e0b-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="75e0b-137">Sia l'API HTTP gRPC che gRPC-Web consentono di chiamare i servizi gRPC da un browser.</span><span class="sxs-lookup"><span data-stu-id="75e0b-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="75e0b-138">Tuttavia, il modo in cui ogni operazione è diversa:</span><span class="sxs-lookup"><span data-stu-id="75e0b-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="75e0b-139">gRPC-Web consente alle app del browser di chiamare i servizi gRPC dal browser con gRPC-Web client e protobuf.</span><span class="sxs-lookup"><span data-stu-id="75e0b-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="75e0b-140">gRPC-Web richiede che l'app browser generi un client gRPC e abbia il vantaggio di inviare messaggi protobuf piccoli e veloci.</span><span class="sxs-lookup"><span data-stu-id="75e0b-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="75e0b-141">l'API HTTP gRPC consente alle app browser di chiamare i servizi gRPC come se fossero API RESTful con JSON.</span><span class="sxs-lookup"><span data-stu-id="75e0b-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="75e0b-142">Non è necessario che l'app browser generi un client gRPC o non conosca alcun valore di gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="75e0b-143">Non viene creato alcun client generato per l'API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="75e0b-144">Il `Greeter` servizio precedente può essere chiamato usando le API JavaScript del browser:</span><span class="sxs-lookup"><span data-stu-id="75e0b-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="75e0b-145">Stato sperimentale</span><span class="sxs-lookup"><span data-stu-id="75e0b-145">Experimental status</span></span>

<span data-ttu-id="75e0b-146">l'API HTTP di gRPC è un esperimento.</span><span class="sxs-lookup"><span data-stu-id="75e0b-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="75e0b-147">Non è completa e non è supportata.</span><span class="sxs-lookup"><span data-stu-id="75e0b-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="75e0b-148">Siamo interessati a questa tecnologia e alla possibilità che gli sviluppatori di app creino rapidamente i servizi gRPC e JSON nello stesso momento.</span><span class="sxs-lookup"><span data-stu-id="75e0b-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="75e0b-149">Non sono previsti impegni per completare l'API HTTP di gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="75e0b-150">Si vuole misurare gli interessi degli sviluppatori nell'API HTTP di gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="75e0b-151">Se l'API HTTP di gRPC è interessante, inviare [commenti e suggerimenti](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="75e0b-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="75e0b-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="75e0b-152">grpc-gateway</span></span>

<span data-ttu-id="75e0b-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) è un'altra tecnologia per la creazione di API JSON RESTful dai servizi di grpc.</span><span class="sxs-lookup"><span data-stu-id="75e0b-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="75e0b-154">Usa le stesse annotazioni *. proto* per eseguire il mapping dei concetti http ai servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="75e0b-155">La differenza principale tra grpc-gateway e l'API HTTP gRPC è grpc-gateway usa la generazione del codice per creare un server proxy inverso.</span><span class="sxs-lookup"><span data-stu-id="75e0b-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="75e0b-156">Il proxy inverso converte le chiamate RESTful in gRPC e quindi le invia al servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="75e0b-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="75e0b-157">Per l'installazione e l'uso di grpc-gateway, vedere il [file Leggimi di grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="75e0b-157">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75e0b-158">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="75e0b-158">Additional resources</span></span>

* [<span data-ttu-id="75e0b-159">documentazione di Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="75e0b-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
