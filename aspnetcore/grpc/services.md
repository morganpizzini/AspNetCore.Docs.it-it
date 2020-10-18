---
title: Creare i servizi e i metodi di gRPC
author: jamesnk
description: Informazioni su come creare i servizi e i metodi gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: c4d37556a3345d275f45c537a40908c5966fe015
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113621"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="2894b-103">Creare i servizi e i metodi di gRPC</span><span class="sxs-lookup"><span data-stu-id="2894b-103">Create gRPC services and methods</span></span>

<span data-ttu-id="2894b-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2894b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="2894b-105">Questo documento illustra come creare i servizi e i metodi di gRPC in C#.</span><span class="sxs-lookup"><span data-stu-id="2894b-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="2894b-106">Gli argomenti includono:</span><span class="sxs-lookup"><span data-stu-id="2894b-106">Topics include:</span></span>

* <span data-ttu-id="2894b-107">Come definire i servizi e i metodi nei file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="2894b-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="2894b-108">Codice generato usando gli strumenti C# di gRPC.</span><span class="sxs-lookup"><span data-stu-id="2894b-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="2894b-109">Implementazione dei servizi e dei metodi di gRPC.</span><span class="sxs-lookup"><span data-stu-id="2894b-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="2894b-110">Creare nuovi servizi gRPC</span><span class="sxs-lookup"><span data-stu-id="2894b-110">Create new gRPC services</span></span>

<span data-ttu-id="2894b-111">i [Servizi gRPC con C#](xref:grpc/basics) hanno introdotto l'approccio di primo contratto di gRPC per lo sviluppo di API.</span><span class="sxs-lookup"><span data-stu-id="2894b-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="2894b-112">I servizi e i messaggi vengono definiti nei file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="2894b-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="2894b-113">Gli strumenti C# generano quindi il codice dai file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="2894b-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="2894b-114">Per gli asset lato server, viene generato un tipo di base astratto per ogni servizio, insieme alle classi per qualsiasi messaggio.</span><span class="sxs-lookup"><span data-stu-id="2894b-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="2894b-115">Il file con *estensione proto* seguente:</span><span class="sxs-lookup"><span data-stu-id="2894b-115">The following *.proto* file:</span></span>

* <span data-ttu-id="2894b-116">Definisce un `Greeter` servizio.</span><span class="sxs-lookup"><span data-stu-id="2894b-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="2894b-117">Il `Greeter` servizio definisce una `SayHello` chiamata.</span><span class="sxs-lookup"><span data-stu-id="2894b-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="2894b-118">`SayHello` Invia un `HelloRequest` messaggio e riceve un `HelloReply` messaggio</span><span class="sxs-lookup"><span data-stu-id="2894b-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="2894b-119">Gli strumenti c# generano il `GreeterBase` tipo di base c#:</span><span class="sxs-lookup"><span data-stu-id="2894b-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="2894b-120">Per impostazione predefinita, l'oggetto generato `GreeterBase` non esegue alcuna operazione.</span><span class="sxs-lookup"><span data-stu-id="2894b-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="2894b-121">Il `SayHello` metodo virtuale restituirà un `UNIMPLEMENTED` errore a tutti i client che lo chiamano.</span><span class="sxs-lookup"><span data-stu-id="2894b-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="2894b-122">Affinché il servizio sia utile, un'app deve creare un'implementazione concreta di `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="2894b-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="2894b-123">L'implementazione del servizio viene registrata con l'app.</span><span class="sxs-lookup"><span data-stu-id="2894b-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="2894b-124">Se il servizio è ospitato da ASP.NET Core gRPC, è necessario aggiungerlo alla pipeline di routing con il `MapGrpcService` metodo.</span><span class="sxs-lookup"><span data-stu-id="2894b-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="2894b-125">Per altre informazioni, vedere <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="2894b-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="2894b-126">Implementare metodi gRPC</span><span class="sxs-lookup"><span data-stu-id="2894b-126">Implement gRPC methods</span></span>

<span data-ttu-id="2894b-127">Un servizio gRPC può disporre di tipi diversi di metodi.</span><span class="sxs-lookup"><span data-stu-id="2894b-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="2894b-128">Il modo in cui i messaggi vengono inviati e ricevuti da un servizio dipende dal tipo di metodo definito.</span><span class="sxs-lookup"><span data-stu-id="2894b-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="2894b-129">I tipi di metodo gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="2894b-129">The gRPC method types are:</span></span>

* <span data-ttu-id="2894b-130">Unario</span><span class="sxs-lookup"><span data-stu-id="2894b-130">Unary</span></span>
* <span data-ttu-id="2894b-131">Streaming Server</span><span class="sxs-lookup"><span data-stu-id="2894b-131">Server streaming</span></span>
* <span data-ttu-id="2894b-132">Flusso client</span><span class="sxs-lookup"><span data-stu-id="2894b-132">Client streaming</span></span>
* <span data-ttu-id="2894b-133">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="2894b-133">Bi-directional streaming</span></span>

<span data-ttu-id="2894b-134">Le chiamate di streaming vengono specificate con la `stream` parola chiave nel file *. proto* .</span><span class="sxs-lookup"><span data-stu-id="2894b-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="2894b-135">`stream` può essere inserito in un messaggio di richiesta di chiamata, in un messaggio di risposta o in entrambi.</span><span class="sxs-lookup"><span data-stu-id="2894b-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="2894b-136">Ogni tipo di chiamata ha una firma del metodo diversa.</span><span class="sxs-lookup"><span data-stu-id="2894b-136">Each call type has a different method signature.</span></span> <span data-ttu-id="2894b-137">Eseguendo l'override dei metodi generati dal tipo di servizio di base astratto in un'implementazione concreta vengono utilizzati gli argomenti corretti e il tipo restituito.</span><span class="sxs-lookup"><span data-stu-id="2894b-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="2894b-138">Metodo unario</span><span class="sxs-lookup"><span data-stu-id="2894b-138">Unary method</span></span>

<span data-ttu-id="2894b-139">Un metodo unario ottiene il messaggio di richiesta come parametro e restituisce la risposta.</span><span class="sxs-lookup"><span data-stu-id="2894b-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="2894b-140">Una chiamata unaria viene completata quando viene restituita la risposta.</span><span class="sxs-lookup"><span data-stu-id="2894b-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="2894b-141">Le chiamate unarie sono le più simili alle [azioni sui controller API Web](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="2894b-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="2894b-142">Un'importante differenza tra i metodi gRPC delle azioni è che i metodi gRPC non sono in grado di associare parti di una richiesta a diversi argomenti del metodo.</span><span class="sxs-lookup"><span data-stu-id="2894b-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="2894b-143">i metodi gRPC hanno sempre un argomento di messaggio per i dati della richiesta in ingresso.</span><span class="sxs-lookup"><span data-stu-id="2894b-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="2894b-144">È comunque possibile inviare più valori a un servizio gRPC rendendoli i campi nel messaggio di richiesta:</span><span class="sxs-lookup"><span data-stu-id="2894b-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="2894b-145">Metodo di streaming del server</span><span class="sxs-lookup"><span data-stu-id="2894b-145">Server streaming method</span></span>

<span data-ttu-id="2894b-146">Un metodo di streaming del server ottiene il messaggio di richiesta come parametro.</span><span class="sxs-lookup"><span data-stu-id="2894b-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="2894b-147">Poiché è possibile trasmettere più messaggi al chiamante, `responseStream.WriteAsync` viene usato per inviare messaggi di risposta.</span><span class="sxs-lookup"><span data-stu-id="2894b-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="2894b-148">Una chiamata di streaming del server è completa quando il metodo restituisce.</span><span class="sxs-lookup"><span data-stu-id="2894b-148">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="2894b-149">Il client non è in grado di inviare messaggi o dati aggiuntivi dopo l'avvio del metodo di streaming del server.</span><span class="sxs-lookup"><span data-stu-id="2894b-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="2894b-150">Alcuni metodi di streaming sono progettati per essere eseguiti per sempre.</span><span class="sxs-lookup"><span data-stu-id="2894b-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="2894b-151">Per i metodi di streaming continuo, un client può annullare la chiamata quando non è più necessaria.</span><span class="sxs-lookup"><span data-stu-id="2894b-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="2894b-152">Quando si verifica l'annullamento, il client invia un segnale al server e viene generato [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="2894b-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="2894b-153">Il `CancellationToken` token deve essere usato nel server con metodi asincroni, in modo che:</span><span class="sxs-lookup"><span data-stu-id="2894b-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="2894b-154">Tutte le operazioni asincrone vengono annullate insieme alla chiamata di streaming.</span><span class="sxs-lookup"><span data-stu-id="2894b-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="2894b-155">Il metodo si chiude rapidamente.</span><span class="sxs-lookup"><span data-stu-id="2894b-155">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="2894b-156">Metodo di streaming client</span><span class="sxs-lookup"><span data-stu-id="2894b-156">Client streaming method</span></span>

<span data-ttu-id="2894b-157">Un metodo di flusso client viene avviato *senza* il metodo che riceve un messaggio.</span><span class="sxs-lookup"><span data-stu-id="2894b-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="2894b-158">Il `requestStream` parametro viene utilizzato per leggere i messaggi dal client.</span><span class="sxs-lookup"><span data-stu-id="2894b-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="2894b-159">Una chiamata di flusso client viene completata quando viene restituito un messaggio di risposta:</span><span class="sxs-lookup"><span data-stu-id="2894b-159">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="2894b-160">Quando si usa C# 8 o versione successiva, `await foreach` è possibile usare la sintassi per leggere i messaggi.</span><span class="sxs-lookup"><span data-stu-id="2894b-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="2894b-161">Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso della richiesta:</span><span class="sxs-lookup"><span data-stu-id="2894b-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="2894b-162">Metodo di streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="2894b-162">Bi-directional streaming method</span></span>

<span data-ttu-id="2894b-163">Un metodo di streaming bidirezionale inizia *senza* il metodo che riceve un messaggio.</span><span class="sxs-lookup"><span data-stu-id="2894b-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="2894b-164">Il `requestStream` parametro viene utilizzato per leggere i messaggi dal client.</span><span class="sxs-lookup"><span data-stu-id="2894b-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="2894b-165">Il metodo può scegliere di inviare messaggi con `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="2894b-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="2894b-166">Una chiamata di flusso bidirezionale è completa quando il metodo restituisce:</span><span class="sxs-lookup"><span data-stu-id="2894b-166">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="2894b-167">Il codice precedente:</span><span class="sxs-lookup"><span data-stu-id="2894b-167">The preceding code:</span></span>

* <span data-ttu-id="2894b-168">Invia una risposta per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="2894b-168">Sends a response for each request.</span></span>
* <span data-ttu-id="2894b-169">È un utilizzo di base dello streaming bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="2894b-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="2894b-170">È possibile supportare scenari più complessi, ad esempio la lettura di richieste e l'invio simultaneo di risposte:</span><span class="sxs-lookup"><span data-stu-id="2894b-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="2894b-171">In un metodo di streaming bidirezionale, il client e il servizio possono inviare messaggi l'uno all'altro in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="2894b-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="2894b-172">La migliore implementazione di un metodo bidirezionale varia a seconda dei requisiti.</span><span class="sxs-lookup"><span data-stu-id="2894b-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="2894b-173">Accedi alle intestazioni delle richieste gRPC</span><span class="sxs-lookup"><span data-stu-id="2894b-173">Access gRPC request headers</span></span>

<span data-ttu-id="2894b-174">Un messaggio di richiesta non è l'unico modo per un client di inviare dati a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="2894b-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="2894b-175">I valori di intestazione sono disponibili in un servizio utilizzando `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="2894b-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="2894b-176">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2894b-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
