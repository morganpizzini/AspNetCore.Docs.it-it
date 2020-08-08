---
title: Chiamare servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 5aca81da34e5ed51b2dc4f404c1ba4d7377a422f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016245"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="0c0b3-103">Chiamare servizi gRPC con il client .NET</span><span class="sxs-lookup"><span data-stu-id="0c0b3-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="0c0b3-104">Una libreria client di .NET gRPC è disponibile nel pacchetto NuGet [.NET. client di gRPC](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="0c0b3-105">Questo documento illustra come:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-105">This document explains how to:</span></span>

* <span data-ttu-id="0c0b3-106">Configurare un client gRPC per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="0c0b3-107">Eseguire chiamate gRPC a metodi unari, streaming server, flusso client e flusso bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="0c0b3-108">Configurare il client gRPC</span><span class="sxs-lookup"><span data-stu-id="0c0b3-108">Configure gRPC client</span></span>

<span data-ttu-id="0c0b3-109">i client gRPC sono tipi di client concreti [generati da file \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="0c0b3-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="0c0b3-110">Il client gRPC concreto dispone di metodi che vengono convertiti nel servizio gRPC nel file \* \* . proto\* .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="0c0b3-111">Un client gRPC viene creato da un canale.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="0c0b3-112">Iniziare usando `GrpcChannel.ForAddress` per creare un canale e quindi usare il canale per creare un client gRPC:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="0c0b3-113">Un canale rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="0c0b3-114">Quando viene creato un canale, questo viene configurato con le opzioni correlate alla chiamata a un servizio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="0c0b3-115">Ad esempio, l'oggetto `HttpClient` utilizzato per effettuare chiamate, la dimensione massima dei messaggi di invio e ricezione e la registrazione possono essere specificati `GrpcChannelOptions` e utilizzati con `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="0c0b3-116">Per un elenco completo delle opzioni, vedere [Opzioni di configurazione client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="0c0b3-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="0c0b3-117">Configurare TLS</span><span class="sxs-lookup"><span data-stu-id="0c0b3-117">Configure TLS</span></span>

<span data-ttu-id="0c0b3-118">Un client gRPC deve usare la stessa sicurezza a livello di connessione del servizio chiamato.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="0c0b3-119">gRPC client Transport Layer Security (TLS) viene configurato quando viene creato il canale gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="0c0b3-120">Un client gRPC genera un errore quando chiama un servizio e la sicurezza a livello di connessione del canale e del servizio non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="0c0b3-121">Per configurare un canale gRPC per l'uso di TLS, assicurarsi che l'indirizzo del server inizi con `https` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="0c0b3-122">Ad esempio, `GrpcChannel.ForAddress("https://localhost:5001")` Usa il protocollo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="0c0b3-123">Il canale gRPC negotates automaticamente una connessione protetta da TLS e usa una connessione sicura per effettuare chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="0c0b3-124">gRPC supporta l'autenticazione dei certificati client su TLS.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="0c0b3-125">Per informazioni sulla configurazione dei certificati client con un canale gRPC, vedere <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="0c0b3-126">Per chiamare servizi gRPC non protetti, assicurarsi che l'indirizzo del server inizi con `http` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="0c0b3-127">Ad esempio, `GrpcChannel.ForAddress("http://localhost:5000")` Usa il protocollo http.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="0c0b3-128">In .NET Core 3,1 o versioni successive è necessaria una configurazione aggiuntiva per [chiamare i servizi gRPC non protetti con il client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="0c0b3-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="0c0b3-129">Prestazioni client</span><span class="sxs-lookup"><span data-stu-id="0c0b3-129">Client performance</span></span>

<span data-ttu-id="0c0b3-130">Prestazioni e utilizzo del canale e del client:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="0c0b3-131">La creazione di un canale può essere un'operazione costosa.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="0c0b3-132">Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in merito alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="0c0b3-133">i client gRPC vengono creati con i canali.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="0c0b3-134">i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="0c0b3-135">È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="0c0b3-136">Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="0c0b3-137">I client creati dal canale possono effettuare più chiamate simultanee.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="0c0b3-138">`GrpcChannel.ForAddress`non è l'unica opzione per la creazione di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="0c0b3-139">Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l' [integrazione di gRPC client Factory](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="0c0b3-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="0c0b3-140">l'integrazione di gRPC con `HttpClientFactory` offre un'alternativa centralizzata alla creazione di client gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="0c0b3-141">La chiamata a gRPC su HTTP/2 con `Grpc.Net.Client` non è attualmente supportata in Novell.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="0c0b3-142">Stiamo lavorando per migliorare il supporto HTTP/2 in una versione futura di Novell.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="0c0b3-143">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) sono valide alternative che funzionano oggi.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="0c0b3-144">Effettuare chiamate a gRPC</span><span class="sxs-lookup"><span data-stu-id="0c0b3-144">Make gRPC calls</span></span>

<span data-ttu-id="0c0b3-145">Una chiamata gRPC viene avviata chiamando un metodo sul client.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="0c0b3-146">Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="0c0b3-147">gRPC dispone di tipi diversi di metodi.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-147">gRPC has different types of methods.</span></span> <span data-ttu-id="0c0b3-148">Il modo in cui il client viene usato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="0c0b3-149">I tipi di metodo gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-149">The gRPC method types are:</span></span>

* <span data-ttu-id="0c0b3-150">Unaria</span><span class="sxs-lookup"><span data-stu-id="0c0b3-150">Unary</span></span>
* <span data-ttu-id="0c0b3-151">Streaming Server</span><span class="sxs-lookup"><span data-stu-id="0c0b3-151">Server streaming</span></span>
* <span data-ttu-id="0c0b3-152">Flusso client</span><span class="sxs-lookup"><span data-stu-id="0c0b3-152">Client streaming</span></span>
* <span data-ttu-id="0c0b3-153">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="0c0b3-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="0c0b3-154">Chiamata unaria</span><span class="sxs-lookup"><span data-stu-id="0c0b3-154">Unary call</span></span>

<span data-ttu-id="0c0b3-155">Una chiamata unaria inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="0c0b3-156">Al termine del servizio viene restituito un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="0c0b3-157">Ogni metodo del servizio unario nel file \* \* . proto\* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="0c0b3-158">Ad esempio, in `GreeterClient` sono disponibili due modi per chiamare `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="0c0b3-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="0c0b3-159">`GreeterClient.SayHelloAsync`: chiama il `Greeter.SayHello` servizio in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="0c0b3-160">Può essere atteso.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-160">Can be awaited.</span></span>
* <span data-ttu-id="0c0b3-161">`GreeterClient.SayHello`: chiama il `Greeter.SayHello` servizio e blocca fino al completamento.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="0c0b3-162">Non usare nel codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="0c0b3-163">Chiamata di streaming del server</span><span class="sxs-lookup"><span data-stu-id="0c0b3-163">Server streaming call</span></span>

<span data-ttu-id="0c0b3-164">Una chiamata di streaming del server inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="0c0b3-165">`ResponseStream.MoveNext()`legge i messaggi trasmessi dal servizio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="0c0b3-166">La chiamata di streaming del server è completata quando `ResponseStream.MoveNext()` restituisce `false` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="0c0b3-167">Quando si usa C# 8 o versione successiva, `await foreach` è possibile usare la sintassi per leggere i messaggi.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="0c0b3-168">Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="0c0b3-169">Chiamata di streaming client</span><span class="sxs-lookup"><span data-stu-id="0c0b3-169">Client streaming call</span></span>

<span data-ttu-id="0c0b3-170">Una chiamata di streaming client viene avviata *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0c0b3-171">Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0c0b3-172">Al termine dell'invio dei messaggi da parte del client, `RequestStream.CompleteAsync` è necessario chiamare per inviare una notifica al servizio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-172">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="0c0b3-173">La chiamata viene completata quando il servizio restituisce un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-173">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="0c0b3-174">Chiamata di streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="0c0b3-174">Bi-directional streaming call</span></span>

<span data-ttu-id="0c0b3-175">Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="0c0b3-176">Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="0c0b3-177">I messaggi trasmessi dal servizio sono accessibili con `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="0c0b3-178">La chiamata di streaming bidirezionale è completa quando `ResponseStream` non ha più messaggi.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="0c0b3-179">Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi l'uno all'altro in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-179">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="0c0b3-180">La migliore logica client per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-180">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="0c0b3-181">Accedi a gRPC Trailers</span><span class="sxs-lookup"><span data-stu-id="0c0b3-181">Access gRPC trailers</span></span>

<span data-ttu-id="0c0b3-182">le chiamate gRPC possono restituire trailer di gRPC.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-182">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="0c0b3-183">i trailer gRPC vengono usati per fornire i metadati nome/valore relativi a una chiamata.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-183">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="0c0b3-184">I trailer forniscono funzionalità simili alle intestazioni HTTP, ma vengono ricevute alla fine della chiamata.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-184">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="0c0b3-185">i trailer gRPC sono accessibili tramite `GetTrailers()` , che restituisce una raccolta di metadati.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-185">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="0c0b3-186">I trailer vengono restituiti al termine della risposta, pertanto è necessario attendere tutti i messaggi di risposta prima di accedere ai trailer.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-186">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="0c0b3-187">Prima di chiamare, le chiamate di flusso unario e client devono essere attese `ResponseAsync` `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="0c0b3-187">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="0c0b3-188">Le chiamate di streaming bidirezionali e del server devono terminare in attesa del flusso di risposta prima di chiamare `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="0c0b3-188">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="0c0b3-189">i trailer gRPC sono accessibili anche da `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="0c0b3-189">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="0c0b3-190">Un servizio può restituire trailer insieme a uno stato di gRPC non OK.</span><span class="sxs-lookup"><span data-stu-id="0c0b3-190">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="0c0b3-191">In questa situazione i trailer vengono recuperati dall'eccezione generata dal client gRPC:</span><span class="sxs-lookup"><span data-stu-id="0c0b3-191">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="0c0b3-192">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0c0b3-192">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
