---
title: Chiamare servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/18/2020
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
uid: grpc/client
ms.openlocfilehash: 39f9b3fde19e31ca970668552e5829308705f513
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97699133"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="2f288-103">Chiamare servizi gRPC con il client .NET</span><span class="sxs-lookup"><span data-stu-id="2f288-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="2f288-104">Una libreria client di .NET gRPC è disponibile nel pacchetto NuGet [.NET. client di gRPC](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="2f288-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="2f288-105">Questo documento illustra come:</span><span class="sxs-lookup"><span data-stu-id="2f288-105">This document explains how to:</span></span>

* <span data-ttu-id="2f288-106">Configurare un client gRPC per chiamare i servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="2f288-107">Eseguire chiamate gRPC a metodi unari, streaming server, flusso client e flusso bidirezionale.</span><span class="sxs-lookup"><span data-stu-id="2f288-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="2f288-108">Configurare il client gRPC</span><span class="sxs-lookup"><span data-stu-id="2f288-108">Configure gRPC client</span></span>

<span data-ttu-id="2f288-109">i client gRPC sono tipi di client concreti [generati da file *\* . proto*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="2f288-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="2f288-110">Il client gRPC concreto dispone di metodi che vengono convertiti nel servizio gRPC nel file *\* . proto* .</span><span class="sxs-lookup"><span data-stu-id="2f288-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="2f288-111">Ad esempio, un servizio chiamato `Greeter` genera un `GreeterClient` tipo con i metodi per chiamare il servizio.</span><span class="sxs-lookup"><span data-stu-id="2f288-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="2f288-112">Un client gRPC viene creato da un canale.</span><span class="sxs-lookup"><span data-stu-id="2f288-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="2f288-113">Iniziare usando `GrpcChannel.ForAddress` per creare un canale e quindi usare il canale per creare un client gRPC:</span><span class="sxs-lookup"><span data-stu-id="2f288-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="2f288-114">Un canale rappresenta una connessione di lunga durata a un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="2f288-115">Quando viene creato un canale, questo viene configurato con le opzioni correlate alla chiamata a un servizio.</span><span class="sxs-lookup"><span data-stu-id="2f288-115">When a channel is created, it's configured with options related to calling a service.</span></span> <span data-ttu-id="2f288-116">Ad esempio, l'oggetto `HttpClient` utilizzato per effettuare chiamate, la dimensione massima dei messaggi di invio e ricezione e la registrazione possono essere specificati `GrpcChannelOptions` e utilizzati con `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="2f288-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="2f288-117">Per un elenco completo delle opzioni, vedere [Opzioni di configurazione client](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="2f288-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="2f288-118">Configurare TLS</span><span class="sxs-lookup"><span data-stu-id="2f288-118">Configure TLS</span></span>

<span data-ttu-id="2f288-119">Un client gRPC deve usare la stessa sicurezza a livello di connessione del servizio chiamato.</span><span class="sxs-lookup"><span data-stu-id="2f288-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="2f288-120">gRPC client Transport Layer Security (TLS) viene configurato quando viene creato il canale gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="2f288-121">Un client gRPC genera un errore quando chiama un servizio e la sicurezza a livello di connessione del canale e del servizio non corrispondono.</span><span class="sxs-lookup"><span data-stu-id="2f288-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="2f288-122">Per configurare un canale gRPC per l'uso di TLS, assicurarsi che l'indirizzo del server inizi con `https` .</span><span class="sxs-lookup"><span data-stu-id="2f288-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="2f288-123">Ad esempio, `GrpcChannel.ForAddress("https://localhost:5001")` Usa il protocollo HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2f288-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="2f288-124">Il canale gRPC negozia automaticamente una connessione protetta da TLS e usa una connessione sicura per effettuare chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="2f288-125">gRPC supporta l'autenticazione dei certificati client su TLS.</span><span class="sxs-lookup"><span data-stu-id="2f288-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="2f288-126">Per informazioni sulla configurazione dei certificati client con un canale gRPC, vedere <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="2f288-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="2f288-127">Per chiamare servizi gRPC non protetti, assicurarsi che l'indirizzo del server inizi con `http` .</span><span class="sxs-lookup"><span data-stu-id="2f288-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="2f288-128">Ad esempio, `GrpcChannel.ForAddress("http://localhost:5000")` Usa il protocollo http.</span><span class="sxs-lookup"><span data-stu-id="2f288-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="2f288-129">In .NET Core 3,1 o versioni successive è necessaria una configurazione aggiuntiva per [chiamare i servizi gRPC non protetti con il client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="2f288-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="2f288-130">Prestazioni client</span><span class="sxs-lookup"><span data-stu-id="2f288-130">Client performance</span></span>

<span data-ttu-id="2f288-131">Prestazioni e utilizzo del canale e del client:</span><span class="sxs-lookup"><span data-stu-id="2f288-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="2f288-132">La creazione di un canale può essere un'operazione costosa.</span><span class="sxs-lookup"><span data-stu-id="2f288-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="2f288-133">Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in merito alle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="2f288-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="2f288-134">i client gRPC vengono creati con i canali.</span><span class="sxs-lookup"><span data-stu-id="2f288-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="2f288-135">i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="2f288-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="2f288-136">È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.</span><span class="sxs-lookup"><span data-stu-id="2f288-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="2f288-137">Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.</span><span class="sxs-lookup"><span data-stu-id="2f288-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="2f288-138">I client creati dal canale possono effettuare più chiamate simultanee.</span><span class="sxs-lookup"><span data-stu-id="2f288-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="2f288-139">`GrpcChannel.ForAddress` non è l'unica opzione per la creazione di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="2f288-140">Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l' [integrazione di gRPC client Factory](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="2f288-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="2f288-141">l'integrazione di gRPC con `HttpClientFactory` offre un'alternativa centralizzata alla creazione di client gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="2f288-142">La chiamata a gRPC su HTTP/2 con `Grpc.Net.Client` non è attualmente supportata in Novell.</span><span class="sxs-lookup"><span data-stu-id="2f288-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="2f288-143">Stiamo lavorando per migliorare il supporto HTTP/2 in una versione futura di Novell.</span><span class="sxs-lookup"><span data-stu-id="2f288-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="2f288-144">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) sono valide alternative che funzionano oggi.</span><span class="sxs-lookup"><span data-stu-id="2f288-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="2f288-145">Effettuare chiamate a gRPC</span><span class="sxs-lookup"><span data-stu-id="2f288-145">Make gRPC calls</span></span>

<span data-ttu-id="2f288-146">Una chiamata gRPC viene avviata chiamando un metodo sul client.</span><span class="sxs-lookup"><span data-stu-id="2f288-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="2f288-147">Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.</span><span class="sxs-lookup"><span data-stu-id="2f288-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="2f288-148">gRPC dispone di tipi diversi di metodi.</span><span class="sxs-lookup"><span data-stu-id="2f288-148">gRPC has different types of methods.</span></span> <span data-ttu-id="2f288-149">Il modo in cui il client viene usato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato.</span><span class="sxs-lookup"><span data-stu-id="2f288-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="2f288-150">I tipi di metodo gRPC sono:</span><span class="sxs-lookup"><span data-stu-id="2f288-150">The gRPC method types are:</span></span>

* <span data-ttu-id="2f288-151">Unaria</span><span class="sxs-lookup"><span data-stu-id="2f288-151">Unary</span></span>
* <span data-ttu-id="2f288-152">Streaming Server</span><span class="sxs-lookup"><span data-stu-id="2f288-152">Server streaming</span></span>
* <span data-ttu-id="2f288-153">Flusso client</span><span class="sxs-lookup"><span data-stu-id="2f288-153">Client streaming</span></span>
* <span data-ttu-id="2f288-154">Streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="2f288-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="2f288-155">Chiamata unaria</span><span class="sxs-lookup"><span data-stu-id="2f288-155">Unary call</span></span>

<span data-ttu-id="2f288-156">Una chiamata unaria inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="2f288-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="2f288-157">Al termine del servizio viene restituito un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="2f288-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="2f288-158">Ogni metodo del servizio unario nel file *\* . proto* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco.</span><span class="sxs-lookup"><span data-stu-id="2f288-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="2f288-159">Ad esempio, in `GreeterClient` sono disponibili due modi per chiamare `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="2f288-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="2f288-160">`GreeterClient.SayHelloAsync` : chiama il `Greeter.SayHello` servizio in modo asincrono.</span><span class="sxs-lookup"><span data-stu-id="2f288-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="2f288-161">Può essere atteso.</span><span class="sxs-lookup"><span data-stu-id="2f288-161">Can be awaited.</span></span>
* <span data-ttu-id="2f288-162">`GreeterClient.SayHello` : chiama il `Greeter.SayHello` servizio e blocca fino al completamento.</span><span class="sxs-lookup"><span data-stu-id="2f288-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="2f288-163">Non usare nel codice asincrono.</span><span class="sxs-lookup"><span data-stu-id="2f288-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="2f288-164">Chiamata di streaming del server</span><span class="sxs-lookup"><span data-stu-id="2f288-164">Server streaming call</span></span>

<span data-ttu-id="2f288-165">Una chiamata di streaming del server inizia con il client che invia un messaggio di richiesta.</span><span class="sxs-lookup"><span data-stu-id="2f288-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="2f288-166">`ResponseStream.MoveNext()` legge i messaggi trasmessi dal servizio.</span><span class="sxs-lookup"><span data-stu-id="2f288-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="2f288-167">La chiamata di streaming del server è completata quando `ResponseStream.MoveNext()` restituisce `false` .</span><span class="sxs-lookup"><span data-stu-id="2f288-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="2f288-168">Quando si usa C# 8 o versione successiva, `await foreach` è possibile usare la sintassi per leggere i messaggi.</span><span class="sxs-lookup"><span data-stu-id="2f288-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="2f288-169">Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:</span><span class="sxs-lookup"><span data-stu-id="2f288-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="2f288-170">Chiamata di streaming client</span><span class="sxs-lookup"><span data-stu-id="2f288-170">Client streaming call</span></span>

<span data-ttu-id="2f288-171">Una chiamata di streaming client viene avviata *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="2f288-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2f288-172">Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="2f288-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2f288-173">Al termine dell'invio dei messaggi da parte del client, `RequestStream.CompleteAsync()` è necessario chiamare per inviare una notifica al servizio.</span><span class="sxs-lookup"><span data-stu-id="2f288-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="2f288-174">La chiamata viene completata quando il servizio restituisce un messaggio di risposta.</span><span class="sxs-lookup"><span data-stu-id="2f288-174">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="2f288-175">Chiamata di streaming bidirezionale</span><span class="sxs-lookup"><span data-stu-id="2f288-175">Bi-directional streaming call</span></span>

<span data-ttu-id="2f288-176">Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio.</span><span class="sxs-lookup"><span data-stu-id="2f288-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="2f288-177">Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="2f288-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="2f288-178">I messaggi trasmessi dal servizio sono accessibili con `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="2f288-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="2f288-179">La chiamata di streaming bidirezionale è completa quando `ResponseStream` non ha più messaggi.</span><span class="sxs-lookup"><span data-stu-id="2f288-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="2f288-180">Per ottenere prestazioni ottimali e per evitare errori non necessari nel client e nel servizio, provare a completare correttamente le chiamate di streaming bidirezionali.</span><span class="sxs-lookup"><span data-stu-id="2f288-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="2f288-181">Una chiamata bidirezionale viene completata correttamente quando il server ha terminato la lettura del flusso di richiesta e il client ha terminato la lettura del flusso di risposta.</span><span class="sxs-lookup"><span data-stu-id="2f288-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="2f288-182">La chiamata di esempio precedente è un esempio di una chiamata bidirezionale che termina normalmente.</span><span class="sxs-lookup"><span data-stu-id="2f288-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="2f288-183">Nella chiamata al client:</span><span class="sxs-lookup"><span data-stu-id="2f288-183">In the call, the client:</span></span>

1. <span data-ttu-id="2f288-184">Avvia una nuova chiamata di flusso bidirezionale chiamando `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="2f288-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="2f288-185">Crea un'attività in background per leggere i messaggi dal servizio usando `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="2f288-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="2f288-186">Invia messaggi al server con `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="2f288-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="2f288-187">Notifica al server che ha terminato l'invio di messaggi `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="2f288-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="2f288-188">Attende che l'attività in background Legga tutti i messaggi in arrivo.</span><span class="sxs-lookup"><span data-stu-id="2f288-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="2f288-189">Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi l'uno all'altro in qualsiasi momento.</span><span class="sxs-lookup"><span data-stu-id="2f288-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="2f288-190">La migliore logica client per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.</span><span class="sxs-lookup"><span data-stu-id="2f288-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-headers"></a><span data-ttu-id="2f288-191">Accedi alle intestazioni gRPC</span><span class="sxs-lookup"><span data-stu-id="2f288-191">Access gRPC headers</span></span>

<span data-ttu-id="2f288-192">gRPC chiama le intestazioni di risposta di restituzione.</span><span class="sxs-lookup"><span data-stu-id="2f288-192">gRPC calls return response headers.</span></span> <span data-ttu-id="2f288-193">Le intestazioni di risposta HTTP passano i metadati nome/valore per una chiamata che non è correlata al messaggio restituito.</span><span class="sxs-lookup"><span data-stu-id="2f288-193">HTTP response headers pass name/value metadata about a call that isn't related the returned message.</span></span>

<span data-ttu-id="2f288-194">Le intestazioni sono accessibili tramite `ResponseHeadersAsync` , che restituisce una raccolta di metadati.</span><span class="sxs-lookup"><span data-stu-id="2f288-194">Headers are accessible using `ResponseHeadersAsync`, which returns a collection of metadata.</span></span> <span data-ttu-id="2f288-195">Le intestazioni vengono in genere restituite con il messaggio di risposta. Pertanto, è necessario attenderli.</span><span class="sxs-lookup"><span data-stu-id="2f288-195">Headers are typically returned with the response message; therefore, you must await them.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });

var headers = await call.ResponseHeadersAsync;
var myValue = headers.GetValue("my-trailer-name");

var response = await call.ResponseAsync;
```

<span data-ttu-id="2f288-196">`ResponseHeadersAsync` utilizzo</span><span class="sxs-lookup"><span data-stu-id="2f288-196">`ResponseHeadersAsync` usage:</span></span>

* <span data-ttu-id="2f288-197">Deve attendere il risultato di `ResponseHeadersAsync` per ottenere la raccolta di intestazioni.</span><span class="sxs-lookup"><span data-stu-id="2f288-197">Must await the result of `ResponseHeadersAsync` to get the headers collection.</span></span>
* <span data-ttu-id="2f288-198">Non è necessario accedere a prima `ResponseAsync` (o al flusso di risposta durante il flusso).</span><span class="sxs-lookup"><span data-stu-id="2f288-198">Doesn't have to be accessed before `ResponseAsync` (or the response stream when streaming).</span></span> <span data-ttu-id="2f288-199">Se è stata restituita una risposta, `ResponseHeadersAsync` restituisce immediatamente le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="2f288-199">If a response has been returned, then `ResponseHeadersAsync` returns headers instantly.</span></span>
* <span data-ttu-id="2f288-200">Genererà un'eccezione se si verifica un errore di connessione o del server e non sono state restituite intestazioni per la chiamata gRPC.</span><span class="sxs-lookup"><span data-stu-id="2f288-200">Will throw an exception if there was a connection or server error and headers weren't returned for the gRPC call.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="2f288-201">Accedi a gRPC Trailers</span><span class="sxs-lookup"><span data-stu-id="2f288-201">Access gRPC trailers</span></span>

<span data-ttu-id="2f288-202">le chiamate gRPC possono restituire trailer di risposta.</span><span class="sxs-lookup"><span data-stu-id="2f288-202">gRPC calls may return response trailers.</span></span> <span data-ttu-id="2f288-203">I trailer vengono utilizzati per fornire i metadati nome/valore relativi a una chiamata.</span><span class="sxs-lookup"><span data-stu-id="2f288-203">Trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="2f288-204">I trailer forniscono funzionalità simili alle intestazioni HTTP, ma vengono ricevute alla fine della chiamata.</span><span class="sxs-lookup"><span data-stu-id="2f288-204">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="2f288-205">I trailer sono accessibili tramite `GetTrailers()` , che restituisce una raccolta di metadati.</span><span class="sxs-lookup"><span data-stu-id="2f288-205">Trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="2f288-206">I trailer vengono restituiti al termine della risposta.</span><span class="sxs-lookup"><span data-stu-id="2f288-206">Trailers are returned after the response is complete.</span></span> <span data-ttu-id="2f288-207">Pertanto, è necessario attendere tutti i messaggi di risposta prima di accedere ai trailer.</span><span class="sxs-lookup"><span data-stu-id="2f288-207">Therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="2f288-208">Prima di chiamare, le chiamate di flusso unario e client devono essere attese `ResponseAsync` `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="2f288-208">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="2f288-209">Le chiamate di streaming bidirezionali e del server devono terminare in attesa del flusso di risposta prima di chiamare `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="2f288-209">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="2f288-210">I trailer sono accessibili anche da `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="2f288-210">Trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="2f288-211">Un servizio può restituire trailer insieme a uno stato di gRPC non OK.</span><span class="sxs-lookup"><span data-stu-id="2f288-211">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="2f288-212">In questa situazione, i trailer vengono recuperati dall'eccezione generata dal client gRPC:</span><span class="sxs-lookup"><span data-stu-id="2f288-212">In this situation, the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="configure-deadline"></a><span data-ttu-id="2f288-213">Configura scadenza</span><span class="sxs-lookup"><span data-stu-id="2f288-213">Configure deadline</span></span>

<span data-ttu-id="2f288-214">È consigliabile configurare una scadenza della chiamata gRPC in quanto fornisce un limite massimo per quanto tempo è possibile eseguire una chiamata per.</span><span class="sxs-lookup"><span data-stu-id="2f288-214">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="2f288-215">Si interrompe l'esecuzione di servizi non confunzionanti e l'esaurimento delle risorse del server.</span><span class="sxs-lookup"><span data-stu-id="2f288-215">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="2f288-216">Le scadenze sono uno strumento utile per la creazione di app affidabili.</span><span class="sxs-lookup"><span data-stu-id="2f288-216">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="2f288-217">Configurare `CallOptions.Deadline` per impostare una scadenza per una chiamata gRPC:</span><span class="sxs-lookup"><span data-stu-id="2f288-217">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="2f288-218">Per altre informazioni, vedere <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="2f288-218">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2f288-219">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2f288-219">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
