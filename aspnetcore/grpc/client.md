---
title: Chiamare servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
uid: grpc/client
ms.openlocfilehash: aefa52a5c4c66178c5978aebd4cd9b00559c7f54
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791554"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="8668a-103">Chiamare servizi gRPC con il client .NET</span><span class="sxs-lookup"><span data-stu-id="8668a-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="8668a-104">Una libreria client gRPC .NET è disponibile nel pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="8668a-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="8668a-105">Questo documento spiega come:</span><span class="sxs-lookup"><span data-stu-id="8668a-105">This document explains how to:</span></span>

* <span data-ttu-id="8668a-106">Configurare un client gRPC per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="8668a-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="8668a-107">Effettuare chiamate gRPC a metodi di streaming unario, streaming server, streaming client e bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="8668a-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="8668a-108">Configurare il client gRPC</span><span class="sxs-lookup"><span data-stu-id="8668a-108">Configure gRPC client</span></span>

<span data-ttu-id="8668a-109">I client gRPC sono tipi di client concreti [generati da \* \*\* file con estensione proto](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="8668a-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="8668a-110">Il client gRPC concreto dispone di metodi che si traducono nel servizio gRPC nel file \* \*proto.\*</span><span class="sxs-lookup"><span data-stu-id="8668a-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="8668a-111">Un client gRPC viene creato da un canale.</span><span class="sxs-lookup"><span data-stu-id="8668a-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="8668a-112">Iniziare utilizzando `GrpcChannel.ForAddress` per creare un canale e quindi utilizzare il canale per creare un client gRPC:</span><span class="sxs-lookup"><span data-stu-id="8668a-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="8668a-113">Un canale rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="8668a-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="8668a-114">Quando viene creato, un canale viene configurato con le opzioni relative alla chiamata di un servizio.</span><span class="sxs-lookup"><span data-stu-id="8668a-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="8668a-115">Ad esempio, `HttpClient` l'oggetto utilizzato per effettuare chiamate, la dimensione `GrpcChannelOptions` massima dei `GrpcChannel.ForAddress`messaggi di invio e ricezione e la registrazione può essere specificata e utilizzata con .</span><span class="sxs-lookup"><span data-stu-id="8668a-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="8668a-116">Per un elenco completo delle opzioni, vedere Opzioni di [configurazione client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="8668a-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="8668a-117">Prestazioni e utilizzo di canali e client:</span><span class="sxs-lookup"><span data-stu-id="8668a-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="8668a-118">La creazione di un canale può essere un'operazione costosa.</span><span class="sxs-lookup"><span data-stu-id="8668a-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="8668a-119">Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in termini di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="8668a-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="8668a-120">i client gRPC vengono creati con i canali.</span><span class="sxs-lookup"><span data-stu-id="8668a-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="8668a-121">I client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="8668a-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="8668a-122">È possibile creare più client gRPC da un canale, inclusi diversi tipi di client.</span><span class="sxs-lookup"><span data-stu-id="8668a-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="8668a-123">Un canale e i client creati dal canale possono essere utilizzati in modo sicuro da più thread.</span><span class="sxs-lookup"><span data-stu-id="8668a-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="8668a-124">I client creati dal canale possono effettuare più chiamate simultanee.</span><span class="sxs-lookup"><span data-stu-id="8668a-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="8668a-125">`GrpcChannel.ForAddress`non è l'unica opzione per la creazione di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="8668a-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="8668a-126">Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l'integrazione di [factory client gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="8668a-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="8668a-127">L'integrazione `HttpClientFactory` gRPC con offre un'alternativa centralizzata alla creazione di client gRPC.</span><span class="sxs-lookup"><span data-stu-id="8668a-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="8668a-128">È necessaria una configurazione aggiuntiva per [chiamare servizi gRPC non sicuri con il client .NET.](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)</span><span class="sxs-lookup"><span data-stu-id="8668a-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="8668a-129">La chiamata di gRPC `Grpc.Net.Client` su HTTP/2 con non è attualmente supportata su Xamarin.</span><span class="sxs-lookup"><span data-stu-id="8668a-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="8668a-130">Stiamo lavorando per migliorare il supporto HTTP/2 in una futura versione di Xamarin.</span><span class="sxs-lookup"><span data-stu-id="8668a-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="8668a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) sono alternative praticabili che funzionano oggi.</span><span class="sxs-lookup"><span data-stu-id="8668a-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="8668a-132">Effettuare chiamate gRPC</span><span class="sxs-lookup"><span data-stu-id="8668a-132">Make gRPC calls</span></span>

<span data-ttu-id="8668a-133">Una chiamata gRPC viene avviata chiamando un metodo sul client.</span><span class="sxs-lookup"><span data-stu-id="8668a-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="8668a-134">Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.</span><span class="sxs-lookup"><span data-stu-id="8668a-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="8668a-135">gRPC ha diversi tipi di metodi.</span><span class="sxs-lookup"><span data-stu-id="8668a-135">gRPC has different types of methods.</span></span> <span data-ttu-id="8668a-136">Il modo in cui il client viene utilizzato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato.</span><span class="sxs-lookup"><span data-stu-id="8668a-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="8668a-137">I tipi di metodo gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="8668a-137">The gRPC method types are:</span></span>

* <span data-ttu-id="8668a-138">Unaria</span><span class="sxs-lookup"><span data-stu-id="8668a-138">Unary</span></span>
* <span data-ttu-id="8668a-139">Streaming server</span><span class="sxs-lookup"><span data-stu-id="8668a-139">Server streaming</span></span>
* <span data-ttu-id="8668a-140">Streaming client</span><span class="sxs-lookup"><span data-stu-id="8668a-140">Client streaming</span></span>
* <span data-ttu-id="8668a-141">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="8668a-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="8668a-142">Chiamata unaria</span><span class="sxs-lookup"><span data-stu-id="8668a-142">Unary call</span></span>

<span data-ttu-id="8668a-143">Una chiamata unaria inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="8668a-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="8668a-144">Al termine del servizio viene restituito un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="8668a-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="8668a-145">Ogni metodo di servizio unario nel file \* \*.proto\* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco.</span><span class="sxs-lookup"><span data-stu-id="8668a-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="8668a-146">Ad esempio, `GreeterClient` su ci sono `SayHello`due modi di chiamare :</span><span class="sxs-lookup"><span data-stu-id="8668a-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="8668a-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` chiama il servizio in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="8668a-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="8668a-148">Ci si può aspettare.</span><span class="sxs-lookup"><span data-stu-id="8668a-148">Can be awaited.</span></span>
* <span data-ttu-id="8668a-149">`GreeterClient.SayHello`- `Greeter.SayHello` chiama il servizio e i blocchi fino al completamento.</span><span class="sxs-lookup"><span data-stu-id="8668a-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="8668a-150">Non usare nel codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="8668a-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="8668a-151">Chiamata in streaming server</span><span class="sxs-lookup"><span data-stu-id="8668a-151">Server streaming call</span></span>

<span data-ttu-id="8668a-152">Una chiamata di streaming server inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="8668a-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="8668a-153">`ResponseStream.MoveNext()`legge i messaggi trasmessi dal servizio.</span><span class="sxs-lookup"><span data-stu-id="8668a-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="8668a-154">La chiamata al flusso `ResponseStream.MoveNext()` `false`del server è completa quando restituisce .</span><span class="sxs-lookup"><span data-stu-id="8668a-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="8668a-155">Quando si usa C '8 o versione successiva, la `await foreach` sintassi può essere utilizzata per leggere i messaggi.</span><span class="sxs-lookup"><span data-stu-id="8668a-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="8668a-156">Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:The extension method reads all messages from the response stream:</span><span class="sxs-lookup"><span data-stu-id="8668a-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="8668a-157">Chiamata in streaming client</span><span class="sxs-lookup"><span data-stu-id="8668a-157">Client streaming call</span></span>

<span data-ttu-id="8668a-158">Una chiamata di streaming client viene *avviata senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="8668a-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="8668a-159">Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con .</span><span class="sxs-lookup"><span data-stu-id="8668a-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="8668a-160">Quando il client ha `RequestStream.CompleteAsync` terminato l'invio dei messaggi, deve essere chiamato per notificare il servizio.</span><span class="sxs-lookup"><span data-stu-id="8668a-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="8668a-161">La chiamata viene terminata quando il servizio restituisce un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="8668a-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="8668a-162">Chiamata in streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="8668a-162">Bi-directional streaming call</span></span>

<span data-ttu-id="8668a-163">Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="8668a-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="8668a-164">Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con .</span><span class="sxs-lookup"><span data-stu-id="8668a-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="8668a-165">I messaggi trasmessi dal `ResponseStream.MoveNext()` servizio `ResponseStream.ReadAllAsync()`sono accessibili con o .</span><span class="sxs-lookup"><span data-stu-id="8668a-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="8668a-166">La chiamata in streaming bidirezionale `ResponseStream` è completa quando non contiene più messaggi.</span><span class="sxs-lookup"><span data-stu-id="8668a-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="8668a-167">Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi tra loro in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="8668a-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="8668a-168">La logica client migliore per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.</span><span class="sxs-lookup"><span data-stu-id="8668a-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="8668a-169">Accedere ai trailer gRPC</span><span class="sxs-lookup"><span data-stu-id="8668a-169">Access gRPC trailers</span></span>

<span data-ttu-id="8668a-170">Le chiamate gRPC possono restituire trailer gRPC.</span><span class="sxs-lookup"><span data-stu-id="8668a-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="8668a-171">i trailer gRPC vengono utilizzati per fornire metadati nome/valore relativi a una chiamata.</span><span class="sxs-lookup"><span data-stu-id="8668a-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="8668a-172">I trailer forniscono funzionalità simili alle intestazioni HTTP, ma vengono ricevuti alla fine della chiamata.</span><span class="sxs-lookup"><span data-stu-id="8668a-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="8668a-173">i trailer gRPC `GetTrailers()`sono accessibili tramite , che restituisce una raccolta di metadati.</span><span class="sxs-lookup"><span data-stu-id="8668a-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="8668a-174">I trailer vengono restituiti al termine della risposta, pertanto è necessario attendere tutti i messaggi di risposta prima di accedere ai trailer.</span><span class="sxs-lookup"><span data-stu-id="8668a-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="8668a-175">Le chiamate in streaming unarie e client devono attendere `ResponseAsync` prima di chiamare: `GetTrailers()`</span><span class="sxs-lookup"><span data-stu-id="8668a-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="8668a-176">Le chiamate di streaming server e bidirezionale devono `GetTrailers()`terminare in attesa del flusso di risposta prima di chiamare :</span><span class="sxs-lookup"><span data-stu-id="8668a-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="8668a-177">i rimorchi gRPC sono `RpcException`accessibili anche da .</span><span class="sxs-lookup"><span data-stu-id="8668a-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="8668a-178">Un servizio può restituire trailer con uno stato gRPC non OK.</span><span class="sxs-lookup"><span data-stu-id="8668a-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="8668a-179">In questo caso i trailer vengono recuperati dall'eccezione generata dal client gRPC:</span><span class="sxs-lookup"><span data-stu-id="8668a-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="8668a-180">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="8668a-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
