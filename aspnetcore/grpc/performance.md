---
title: Procedure consigliate per le prestazioni in gRPC per ASP.NET Core
author: jamesnk
description: Informazioni sulle procedure consigliate per la creazione di servizi gRPC ad alte prestazioni.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876724"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="92c8b-103">Procedure consigliate per le prestazioni in gRPC per ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92c8b-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="92c8b-104">Di [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="92c8b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="92c8b-105">gRPC è progettato per servizi a prestazioni elevate.</span><span class="sxs-lookup"><span data-stu-id="92c8b-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="92c8b-106">Questo documento illustra come ottenere le migliori prestazioni possibili da gRPC.</span><span class="sxs-lookup"><span data-stu-id="92c8b-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="92c8b-107">Riutilizza canale</span><span class="sxs-lookup"><span data-stu-id="92c8b-107">Reuse channel</span></span>

<span data-ttu-id="92c8b-108">Quando si effettuano chiamate gRPC è necessario riutilizzare un canale gRPC.</span><span class="sxs-lookup"><span data-stu-id="92c8b-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="92c8b-109">Il riutilizzo di un canale consente il multiplexing delle chiamate tramite una connessione HTTP/2 esistente.</span><span class="sxs-lookup"><span data-stu-id="92c8b-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="92c8b-110">Se viene creato un nuovo canale per ogni chiamata gRPC, la quantità di tempo necessaria per il completamento può aumentare in modo significativo.</span><span class="sxs-lookup"><span data-stu-id="92c8b-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="92c8b-111">Per ogni chiamata sono necessari più round trip di rete tra il client e il server per creare una connessione HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="92c8b-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="92c8b-112">Apertura di un socket</span><span class="sxs-lookup"><span data-stu-id="92c8b-112">Opening a socket</span></span>
2. <span data-ttu-id="92c8b-113">Creazione della connessione TCP</span><span class="sxs-lookup"><span data-stu-id="92c8b-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="92c8b-114">Negoziazione di TLS</span><span class="sxs-lookup"><span data-stu-id="92c8b-114">Negotiating TLS</span></span>
4. <span data-ttu-id="92c8b-115">Avvio della connessione HTTP/2</span><span class="sxs-lookup"><span data-stu-id="92c8b-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="92c8b-116">Creazione della chiamata gRPC</span><span class="sxs-lookup"><span data-stu-id="92c8b-116">Making the gRPC call</span></span>

<span data-ttu-id="92c8b-117">I canali possono essere condivisi e riutilizzati in modo sicuro tra le chiamate a gRPC:</span><span class="sxs-lookup"><span data-stu-id="92c8b-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="92c8b-118">i client gRPC vengono creati con i canali.</span><span class="sxs-lookup"><span data-stu-id="92c8b-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="92c8b-119">i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.</span><span class="sxs-lookup"><span data-stu-id="92c8b-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="92c8b-120">È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.</span><span class="sxs-lookup"><span data-stu-id="92c8b-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="92c8b-121">Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.</span><span class="sxs-lookup"><span data-stu-id="92c8b-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="92c8b-122">I client creati dal canale possono effettuare più chiamate simultanee.</span><span class="sxs-lookup"><span data-stu-id="92c8b-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="92c8b-123">Concorrenza di connessione</span><span class="sxs-lookup"><span data-stu-id="92c8b-123">Connection concurrency</span></span>

<span data-ttu-id="92c8b-124">Le connessioni HTTP/2 in genere presentano un limite al numero [massimo di flussi simultanei (richieste HTTP attive)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) in una connessione in una sola volta.</span><span class="sxs-lookup"><span data-stu-id="92c8b-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="92c8b-125">Per impostazione predefinita, la maggior parte dei server imposta questo limite su 100 flussi simultanei.</span><span class="sxs-lookup"><span data-stu-id="92c8b-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="92c8b-126">Un canale gRPC usa una singola connessione HTTP/2 e le chiamate simultanee vengono multiplexate su tale connessione.</span><span class="sxs-lookup"><span data-stu-id="92c8b-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="92c8b-127">Quando il numero di chiamate attive raggiunge il limite del flusso di connessione, le chiamate aggiuntive vengono accodate nel client.</span><span class="sxs-lookup"><span data-stu-id="92c8b-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="92c8b-128">Le chiamate in coda attendono il completamento delle chiamate attive prima di essere inviate.</span><span class="sxs-lookup"><span data-stu-id="92c8b-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="92c8b-129">Le applicazioni con carico elevato, o chiamate gRPC di streaming con esecuzione prolungata, potrebbero riscontrare problemi di prestazioni causati dall'accodamento delle chiamate a causa di questo limite.</span><span class="sxs-lookup"><span data-stu-id="92c8b-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="92c8b-130">In .NET 5 è stata introdotta la `SocketsHttpHandler.EnableMultipleHttp2Connections` Proprietà.</span><span class="sxs-lookup"><span data-stu-id="92c8b-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="92c8b-131">Quando è impostato su `true` , le connessioni http/2 aggiuntive vengono create da un canale quando viene raggiunto il limite di flusso simultaneo.</span><span class="sxs-lookup"><span data-stu-id="92c8b-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="92c8b-132">Quando `GrpcChannel` viene creato un oggetto, il relativo interno `SocketsHttpHandler` viene configurato automaticamente per creare connessioni http/2 aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="92c8b-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="92c8b-133">Se un'app configura il proprio gestore, provare a impostare `EnableMultipleHttp2Connections` su `true` :</span><span class="sxs-lookup"><span data-stu-id="92c8b-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="92c8b-134">Esistono un paio di soluzioni alternative per le app .NET Core 3,1:</span><span class="sxs-lookup"><span data-stu-id="92c8b-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="92c8b-135">Creare canali gRPC distinti per le aree dell'app con carico elevato.</span><span class="sxs-lookup"><span data-stu-id="92c8b-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="92c8b-136">Ad esempio, il `Logger` servizio gRPC potrebbe avere un carico elevato.</span><span class="sxs-lookup"><span data-stu-id="92c8b-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="92c8b-137">Usare un canale separato per creare il `LoggerClient` nell'app.</span><span class="sxs-lookup"><span data-stu-id="92c8b-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="92c8b-138">Usare un pool di canali gRPC, ad esempio, creare un elenco di canali gRPC.</span><span class="sxs-lookup"><span data-stu-id="92c8b-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="92c8b-139">`Random` viene usato per selezionare un canale dall'elenco ogni volta che è necessario un canale gRPC.</span><span class="sxs-lookup"><span data-stu-id="92c8b-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="92c8b-140">L'uso di `Random` distribuisce in modo casuale le chiamate su più connessioni.</span><span class="sxs-lookup"><span data-stu-id="92c8b-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="92c8b-141">Aumentare il limite massimo di flussi simultanei sul server è un altro modo per risolvere questo problema.</span><span class="sxs-lookup"><span data-stu-id="92c8b-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="92c8b-142">In gheppio è configurato con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="92c8b-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="92c8b-143">Non è consigliabile aumentare il limite massimo di flussi simultanei.</span><span class="sxs-lookup"><span data-stu-id="92c8b-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="92c8b-144">Un numero eccessivo di flussi in una singola connessione HTTP/2 introduce nuovi problemi di prestazioni:</span><span class="sxs-lookup"><span data-stu-id="92c8b-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="92c8b-145">Contesa di thread tra i flussi che tentano di scrivere nella connessione.</span><span class="sxs-lookup"><span data-stu-id="92c8b-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="92c8b-146">La perdita di pacchetti di connessione causa il blocco di tutte le chiamate a livello TCP.</span><span class="sxs-lookup"><span data-stu-id="92c8b-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="92c8b-147">Ping keep-alive</span><span class="sxs-lookup"><span data-stu-id="92c8b-147">Keep alive pings</span></span>

<span data-ttu-id="92c8b-148">È possibile usare i ping keep-alive per tenere attive le connessioni HTTP/2 durante i periodi di inattività.</span><span class="sxs-lookup"><span data-stu-id="92c8b-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="92c8b-149">Con una connessione HTTP/2 esistente pronta quando un'attività riprende l'attività consente di eseguire rapidamente le chiamate gRPC iniziali, senza un ritardo causato dalla reimpostazione della connessione.</span><span class="sxs-lookup"><span data-stu-id="92c8b-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="92c8b-150">I ping Keep-Alive sono configurati in <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="92c8b-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="92c8b-151">Il codice precedente configura un canale che invia un ping keep-alive al server ogni 60 secondi durante i periodi di inattività.</span><span class="sxs-lookup"><span data-stu-id="92c8b-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="92c8b-152">Il ping garantisce che il server e gli eventuali proxy in uso non chiudano la connessione a causa di inattività.</span><span class="sxs-lookup"><span data-stu-id="92c8b-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="92c8b-153">Streaming</span><span class="sxs-lookup"><span data-stu-id="92c8b-153">Streaming</span></span>

<span data-ttu-id="92c8b-154">il flusso bidirezionale gRPC può essere usato per sostituire le chiamate gRPC unarie in scenari a prestazioni elevate.</span><span class="sxs-lookup"><span data-stu-id="92c8b-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="92c8b-155">Una volta avviato un flusso bidirezionale, lo streaming dei messaggi è più veloce rispetto all'invio di messaggi con più chiamate gRPC unarie.</span><span class="sxs-lookup"><span data-stu-id="92c8b-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="92c8b-156">I messaggi trasmessi come dati in una richiesta HTTP/2 esistente e eliminano il sovraccarico della creazione di una nuova richiesta HTTP/2 per ogni chiamata unaria.</span><span class="sxs-lookup"><span data-stu-id="92c8b-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="92c8b-157">Servizio di esempio:</span><span class="sxs-lookup"><span data-stu-id="92c8b-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="92c8b-158">Client di esempio:</span><span class="sxs-lookup"><span data-stu-id="92c8b-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="92c8b-159">La sostituzione di chiamate unarie con flusso bidirezionale per motivi di prestazioni è una tecnica avanzata e non è appropriata in molte situazioni.</span><span class="sxs-lookup"><span data-stu-id="92c8b-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="92c8b-160">L'uso delle chiamate di streaming è una scelta ottimale nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="92c8b-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="92c8b-161">È necessaria una velocità effettiva elevata o bassa latenza.</span><span class="sxs-lookup"><span data-stu-id="92c8b-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="92c8b-162">gRPC e HTTP/2 sono identificati come un collo di bottiglia delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="92c8b-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="92c8b-163">Un thread di lavoro nel client sta inviando o ricevendo messaggi regolari con un servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="92c8b-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="92c8b-164">Tenere presente la complessità e le limitazioni aggiuntive dell'utilizzo di chiamate di streaming anziché unario:</span><span class="sxs-lookup"><span data-stu-id="92c8b-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="92c8b-165">Un flusso può essere interrotto da un errore del servizio o della connessione.</span><span class="sxs-lookup"><span data-stu-id="92c8b-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="92c8b-166">La logica è necessaria per riavviare il flusso se si verifica un errore.</span><span class="sxs-lookup"><span data-stu-id="92c8b-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="92c8b-167">`RequestStream.WriteAsync` non è sicuro per il multithread.</span><span class="sxs-lookup"><span data-stu-id="92c8b-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="92c8b-168">Solo un messaggio può essere scritto in un flusso alla volta.</span><span class="sxs-lookup"><span data-stu-id="92c8b-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="92c8b-169">L'invio di messaggi da più thread su un singolo flusso richiede una coda producer/consumer <xref:System.Threading.Channels.Channel%601> , ad esempio i messaggi Marshall.</span><span class="sxs-lookup"><span data-stu-id="92c8b-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="92c8b-170">Un metodo di streaming gRPC è limitato a ricevere un tipo di messaggio e a inviare un tipo di messaggio.</span><span class="sxs-lookup"><span data-stu-id="92c8b-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="92c8b-171">Ad esempio, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` riceve `RequestMessage` e invia `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="92c8b-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="92c8b-172">Il supporto di protobuf per i messaggi sconosciuti o condizionali `Any` che usano e `oneof` può ovviare a questa limitazione.</span><span class="sxs-lookup"><span data-stu-id="92c8b-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
