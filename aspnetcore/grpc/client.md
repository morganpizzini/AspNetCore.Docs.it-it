---
title: Chiamare i servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667174"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="6acbe-103">Chiamare i servizi gRPC con il client .NET</span><span class="sxs-lookup"><span data-stu-id="6acbe-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="6acbe-104">Una libreria client gRPC .NET è disponibile nel pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6acbe-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="6acbe-105">Questo documento spiega come:</span><span class="sxs-lookup"><span data-stu-id="6acbe-105">This document explains how to:</span></span>

* <span data-ttu-id="6acbe-106">Configurare un client gRPC per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="6acbe-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="6acbe-107">Effettuare chiamate gRPC a metodi di streaming unario, streaming server, streaming client e bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="6acbe-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="6acbe-108">Configurare il client gRPC</span><span class="sxs-lookup"><span data-stu-id="6acbe-108">Configure gRPC client</span></span>

<span data-ttu-id="6acbe-109">I client gRPC sono tipi di client concreti [generati da \* \*\* file con estensione proto](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="6acbe-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="6acbe-110">Il client gRPC concreto dispone di metodi che si traducono nel servizio gRPC nel file \* \*proto.\*</span><span class="sxs-lookup"><span data-stu-id="6acbe-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="6acbe-111">Un client gRPC viene creato da un canale.</span><span class="sxs-lookup"><span data-stu-id="6acbe-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="6acbe-112">Iniziare utilizzando `GrpcChannel.ForAddress` per creare un canale e quindi utilizzare il canale per creare un client gRPC:</span><span class="sxs-lookup"><span data-stu-id="6acbe-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="6acbe-113">Un canale rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="6acbe-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="6acbe-114">Quando viene creato, un canale viene configurato con le opzioni relative alla chiamata di un servizio.</span><span class="sxs-lookup"><span data-stu-id="6acbe-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="6acbe-115">Ad esempio, `HttpClient` l'oggetto utilizzato per effettuare chiamate, la dimensione `GrpcChannelOptions` massima dei `GrpcChannel.ForAddress`messaggi di invio e ricezione e la registrazione può essere specificata e utilizzata con .</span><span class="sxs-lookup"><span data-stu-id="6acbe-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="6acbe-116">Per un elenco completo delle opzioni, vedere Opzioni di [configurazione client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="6acbe-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="6acbe-117">Prestazioni e utilizzo di canali e client:</span><span class="sxs-lookup"><span data-stu-id="6acbe-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="6acbe-118">La creazione di un canale può essere un'operazione costosa.</span><span class="sxs-lookup"><span data-stu-id="6acbe-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="6acbe-119">Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in termini di prestazioni.</span><span class="sxs-lookup"><span data-stu-id="6acbe-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="6acbe-120">i client gRPC vengono creati con i canali.</span><span class="sxs-lookup"><span data-stu-id="6acbe-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="6acbe-121">I client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="6acbe-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="6acbe-122">È possibile creare più client gRPC da un canale, inclusi diversi tipi di client.</span><span class="sxs-lookup"><span data-stu-id="6acbe-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="6acbe-123">Un canale e i client creati dal canale possono essere utilizzati in modo sicuro da più thread.</span><span class="sxs-lookup"><span data-stu-id="6acbe-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="6acbe-124">I client creati dal canale possono effettuare più chiamate simultanee.</span><span class="sxs-lookup"><span data-stu-id="6acbe-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="6acbe-125">`GrpcChannel.ForAddress`non è l'unica opzione per la creazione di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="6acbe-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="6acbe-126">Se si chiamano servizi gRPC da un'app ASP.NET Core, prendere in considerazione l'integrazione di [fabbrica client gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="6acbe-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="6acbe-127">L'integrazione `HttpClientFactory` gRPC con offre un'alternativa centralizzata alla creazione di client gRPC.</span><span class="sxs-lookup"><span data-stu-id="6acbe-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="6acbe-128">È necessaria una configurazione aggiuntiva per [chiamare servizi gRPC non sicuri con il client .NET.](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)</span><span class="sxs-lookup"><span data-stu-id="6acbe-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="6acbe-129">La chiamata di gRPC `Grpc.Net.Client` su HTTP/2 con non è attualmente supportata su Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6acbe-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="6acbe-130">Stiamo lavorando per migliorare il supporto HTTP/2 in una futura versione di Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6acbe-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="6acbe-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) sono alternative praticabili che funzionano oggi.</span><span class="sxs-lookup"><span data-stu-id="6acbe-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="6acbe-132">Effettuare chiamate gRPC</span><span class="sxs-lookup"><span data-stu-id="6acbe-132">Make gRPC calls</span></span>

<span data-ttu-id="6acbe-133">Una chiamata gRPC viene avviata chiamando un metodo sul client.</span><span class="sxs-lookup"><span data-stu-id="6acbe-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="6acbe-134">Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.</span><span class="sxs-lookup"><span data-stu-id="6acbe-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="6acbe-135">gRPC ha diversi tipi di metodi.</span><span class="sxs-lookup"><span data-stu-id="6acbe-135">gRPC has different types of methods.</span></span> <span data-ttu-id="6acbe-136">La modalità di utilizzo del client per effettuare una chiamata gRPC dipende dal tipo di metodo che si sta chiamando.</span><span class="sxs-lookup"><span data-stu-id="6acbe-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="6acbe-137">I tipi di metodo gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="6acbe-137">The gRPC method types are:</span></span>

* <span data-ttu-id="6acbe-138">Unaria</span><span class="sxs-lookup"><span data-stu-id="6acbe-138">Unary</span></span>
* <span data-ttu-id="6acbe-139">Streaming server</span><span class="sxs-lookup"><span data-stu-id="6acbe-139">Server streaming</span></span>
* <span data-ttu-id="6acbe-140">Streaming client</span><span class="sxs-lookup"><span data-stu-id="6acbe-140">Client streaming</span></span>
* <span data-ttu-id="6acbe-141">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="6acbe-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="6acbe-142">Chiamata unaria</span><span class="sxs-lookup"><span data-stu-id="6acbe-142">Unary call</span></span>

<span data-ttu-id="6acbe-143">Una chiamata unaria inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="6acbe-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="6acbe-144">Al termine del servizio viene restituito un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="6acbe-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="6acbe-145">Ogni metodo di servizio unario nel file \* \*.proto\* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco.</span><span class="sxs-lookup"><span data-stu-id="6acbe-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="6acbe-146">Ad esempio, `GreeterClient` su ci sono `SayHello`due modi di chiamare :</span><span class="sxs-lookup"><span data-stu-id="6acbe-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="6acbe-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` chiama il servizio in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="6acbe-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="6acbe-148">Ci si può aspettare.</span><span class="sxs-lookup"><span data-stu-id="6acbe-148">Can be awaited.</span></span>
* <span data-ttu-id="6acbe-149">`GreeterClient.SayHello`- `Greeter.SayHello` chiama il servizio e i blocchi fino al completamento.</span><span class="sxs-lookup"><span data-stu-id="6acbe-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="6acbe-150">Non usare nel codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="6acbe-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="6acbe-151">Chiamata in streaming server</span><span class="sxs-lookup"><span data-stu-id="6acbe-151">Server streaming call</span></span>

<span data-ttu-id="6acbe-152">Una chiamata di streaming server inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="6acbe-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="6acbe-153">`ResponseStream.MoveNext()`legge i messaggi trasmessi dal servizio.</span><span class="sxs-lookup"><span data-stu-id="6acbe-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="6acbe-154">La chiamata al flusso `ResponseStream.MoveNext()` `false`del server è completa quando restituisce .</span><span class="sxs-lookup"><span data-stu-id="6acbe-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="6acbe-155">Se si utilizza C , 8 `await foreach` o versione successiva, è possibile utilizzare la sintassi per leggere i messaggi.</span><span class="sxs-lookup"><span data-stu-id="6acbe-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="6acbe-156">Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:The extension method reads all messages from the response stream:</span><span class="sxs-lookup"><span data-stu-id="6acbe-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="6acbe-157">Chiamata in streaming client</span><span class="sxs-lookup"><span data-stu-id="6acbe-157">Client streaming call</span></span>

<span data-ttu-id="6acbe-158">Una chiamata di streaming client viene *avviata senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="6acbe-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6acbe-159">Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con .</span><span class="sxs-lookup"><span data-stu-id="6acbe-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6acbe-160">Al termine dell'invio `RequestStream.CompleteAsync` dei messaggi da parte del client, è necessario chiamare il client.</span><span class="sxs-lookup"><span data-stu-id="6acbe-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="6acbe-161">La chiamata viene terminata quando il servizio restituisce un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="6acbe-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="6acbe-162">Chiamata in streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="6acbe-162">Bi-directional streaming call</span></span>

<span data-ttu-id="6acbe-163">Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="6acbe-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6acbe-164">Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con .</span><span class="sxs-lookup"><span data-stu-id="6acbe-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6acbe-165">I messaggi trasmessi dal `ResponseStream.MoveNext()` servizio `ResponseStream.ReadAllAsync()`sono accessibili con o .</span><span class="sxs-lookup"><span data-stu-id="6acbe-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="6acbe-166">La chiamata in streaming bidirezionale `ResponseStream` è completa quando non contiene più messaggi.</span><span class="sxs-lookup"><span data-stu-id="6acbe-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
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
}
```

<span data-ttu-id="6acbe-167">Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi tra loro in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="6acbe-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="6acbe-168">La logica client migliore per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.</span><span class="sxs-lookup"><span data-stu-id="6acbe-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6acbe-169">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6acbe-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
