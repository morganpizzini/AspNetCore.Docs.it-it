---
title: 'Usare il flusso in ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: Informazioni su come trasmettere i dati tra il client e il server.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690629"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="b2e10-103">Usare il flusso in ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="b2e10-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="b2e10-104">Di [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="b2e10-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2e10-105">ASP.NET Core :::no-loc(SignalR)::: supporta lo streaming da client a server e da server a client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="b2e10-106">Questa operazione è utile per gli scenari in cui i frammenti di dati arrivano nel tempo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="b2e10-107">Quando si esegue il flusso, ogni frammento viene inviato al client o al server non appena diventa disponibile, anziché attendere che tutti i dati diventino disponibili.</span><span class="sxs-lookup"><span data-stu-id="b2e10-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2e10-108">ASP.NET Core :::no-loc(SignalR)::: supporta i valori restituiti di streaming dei metodi del server.</span><span class="sxs-lookup"><span data-stu-id="b2e10-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="b2e10-109">Questa operazione è utile per gli scenari in cui i frammenti di dati arrivano nel tempo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="b2e10-110">Quando un valore restituito viene trasmesso al client, ogni frammento viene inviato al client non appena diventa disponibile, anziché attendere che tutti i dati diventino disponibili.</span><span class="sxs-lookup"><span data-stu-id="b2e10-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="b2e10-111">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b2e10-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="b2e10-112">Configurare un hub per lo streaming</span><span class="sxs-lookup"><span data-stu-id="b2e10-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2e10-113">Un metodo Hub diventa automaticamente un metodo dell'hub di flusso quando restituisce <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` o `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b2e10-114">Un metodo Hub diventa automaticamente un metodo dell'hub di flusso quando restituisce <xref:System.Threading.Channels.ChannelReader%601> o `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="b2e10-115">Streaming da server a client</span><span class="sxs-lookup"><span data-stu-id="b2e10-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2e10-116">I metodi dell'hub di streaming possono restituire `IAsyncEnumerable<T>` oltre a `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="b2e10-117">Il modo più semplice per restituire `IAsyncEnumerable<T>` consiste nel rendere il metodo dell'hub un metodo iteratore asincrono come illustrato nell'esempio seguente.</span><span class="sxs-lookup"><span data-stu-id="b2e10-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="b2e10-118">I metodi iteratori asincroni dell'hub possono accettare un `CancellationToken` parametro che viene attivato quando il client Annulla la sottoscrizione dal flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="b2e10-119">I metodi iterator asincroni evitano problemi comuni con i canali, ad esempio la mancata restituzione del tempo `ChannelReader` sufficiente o l'uscita dal metodo senza completare <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="b2e10-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="b2e10-120">Nell'esempio seguente vengono illustrate le nozioni di base per il flusso di dati al client tramite canali.</span><span class="sxs-lookup"><span data-stu-id="b2e10-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="b2e10-121">Ogni volta che un oggetto viene scritto in <xref:System.Threading.Channels.ChannelWriter%601> , l'oggetto viene immediatamente inviato al client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="b2e10-122">Alla fine, `ChannelWriter` viene completata per indicare al client che il flusso è chiuso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="b2e10-123">Scrivere in `ChannelWriter<T>` in un thread in background e restituire il appena `ChannelReader` possibile.</span><span class="sxs-lookup"><span data-stu-id="b2e10-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="b2e10-124">Altre chiamate all'hub vengono bloccate fino a quando non `ChannelReader` viene restituito un oggetto.</span><span class="sxs-lookup"><span data-stu-id="b2e10-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="b2e10-125">Eseguire il wrapping della logica in un' [ `try ... catch` istruzione](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="b2e10-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="b2e10-126">Completare `Channel` in un [ `finally` blocco](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="b2e10-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="b2e10-127">Se si vuole propagare un errore, acquisirlo all'interno del `catch` blocco e scriverlo nel `finally` blocco.</span><span class="sxs-lookup"><span data-stu-id="b2e10-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2e10-128">I metodi dell'hub di streaming da server a client possono accettare un `CancellationToken` parametro che viene attivato quando il client Annulla la sottoscrizione dal flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="b2e10-129">Usare questo token per arrestare l'operazione server e rilasciare tutte le risorse se il client si disconnette prima della fine del flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b2e10-130">Streaming da client a server</span><span class="sxs-lookup"><span data-stu-id="b2e10-130">Client-to-server streaming</span></span>

<span data-ttu-id="b2e10-131">Un metodo Hub diventa automaticamente un metodo dell'hub di streaming da client a server quando accetta uno o più oggetti di tipo <xref:System.Threading.Channels.ChannelReader%601> o <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="b2e10-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="b2e10-132">Nell'esempio seguente vengono illustrate le nozioni di base per la lettura dei dati di streaming inviati dal client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="b2e10-133">Ogni volta che il client scrive in <xref:System.Threading.Channels.ChannelWriter%601> , i dati vengono scritti in `ChannelReader` sul server da cui viene letto il metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2e10-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="b2e10-134"><xref:System.Collections.Generic.IAsyncEnumerable%601>Segue una versione del metodo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="b2e10-135">Client .NET</span><span class="sxs-lookup"><span data-stu-id="b2e10-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b2e10-136">Streaming da server a client</span><span class="sxs-lookup"><span data-stu-id="b2e10-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b2e10-137">I `StreamAsync` `StreamAsChannelAsync` metodi e su `HubConnection` vengono usati per richiamare i metodi di streaming da server a client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="b2e10-138">Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsync` o `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b2e10-139">Il parametro generico in `StreamAsync<T>` e `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming.</span><span class="sxs-lookup"><span data-stu-id="b2e10-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b2e10-140">Un oggetto di tipo `IAsyncEnumerable<T>` o `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="b2e10-141">`StreamAsync`Esempio che restituisce `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="b2e10-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="b2e10-142">Un `StreamAsChannelAsync` esempio corrispondente che restituisce `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="b2e10-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b2e10-143">Il `StreamAsChannelAsync` metodo su `HubConnection` viene usato per richiamare un metodo di streaming da server a client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="b2e10-144">Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b2e10-145">Il parametro generico in `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming.</span><span class="sxs-lookup"><span data-stu-id="b2e10-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b2e10-146">Un oggetto `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b2e10-147">Il `StreamAsChannelAsync` metodo su `HubConnection` viene usato per richiamare un metodo di streaming da server a client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="b2e10-148">Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="b2e10-149">Il parametro generico in `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming.</span><span class="sxs-lookup"><span data-stu-id="b2e10-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="b2e10-150">Un oggetto `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b2e10-151">Streaming da client a server</span><span class="sxs-lookup"><span data-stu-id="b2e10-151">Client-to-server streaming</span></span>

<span data-ttu-id="b2e10-152">Esistono due modi per richiamare un metodo dell'hub di streaming da client a server dal client .NET.</span><span class="sxs-lookup"><span data-stu-id="b2e10-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="b2e10-153">È possibile passare un oggetto `IAsyncEnumerable<T>` o `ChannelReader` come argomento a `SendAsync` , `InvokeAsync` o `StreamAsChannelAsync` , a seconda del metodo dell'hub richiamato.</span><span class="sxs-lookup"><span data-stu-id="b2e10-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="b2e10-154">Ogni volta che i dati vengono scritti nell' `IAsyncEnumerable` `ChannelWriter` oggetto o, il metodo dell'hub sul server riceve un nuovo elemento con i dati del client.</span><span class="sxs-lookup"><span data-stu-id="b2e10-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="b2e10-155">Se si usa un `IAsyncEnumerable` oggetto, il flusso termina dopo che il metodo che restituisce gli elementi del flusso viene chiuso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="b2e10-156">In alternativa, se si usa un `ChannelWriter` , è necessario completare il canale con `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="b2e10-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="b2e10-157">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="b2e10-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b2e10-158">Streaming da server a client</span><span class="sxs-lookup"><span data-stu-id="b2e10-158">Server-to-client streaming</span></span>

<span data-ttu-id="b2e10-159">I client JavaScript chiamano i metodi di streaming da server a client negli hub con `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="b2e10-160">Il `stream` metodo accetta due argomenti:</span><span class="sxs-lookup"><span data-stu-id="b2e10-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="b2e10-161">Nome del metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2e10-161">The name of the hub method.</span></span> <span data-ttu-id="b2e10-162">Nell'esempio seguente il nome del metodo dell'hub è `Counter` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="b2e10-163">Argomenti definiti nel metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2e10-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="b2e10-164">Nell'esempio seguente gli argomenti sono un conteggio per il numero di elementi del flusso da ricevere e il ritardo tra gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="b2e10-165">`connection.stream` Restituisce un oggetto `IStreamResult` che contiene un `subscribe` metodo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="b2e10-166">Passare un oggetto `IStreamSubscriber` a `subscribe` e impostare `next` i `error` callback, e `complete` per ricevere notifiche dalla `stream` chiamata.</span><span class="sxs-lookup"><span data-stu-id="b2e10-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="b2e10-167">Per terminare il flusso dal client, chiamare il `dispose` metodo sull'oggetto `ISubscription` restituito dal `subscribe` metodo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="b2e10-168">La chiamata a questo metodo provoca l'annullamento del `CancellationToken` parametro del metodo Hub, se ne è stato specificato uno.</span><span class="sxs-lookup"><span data-stu-id="b2e10-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="b2e10-169">Per terminare il flusso dal client, chiamare il `dispose` metodo sull'oggetto `ISubscription` restituito dal `subscribe` metodo.</span><span class="sxs-lookup"><span data-stu-id="b2e10-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="b2e10-170">Streaming da client a server</span><span class="sxs-lookup"><span data-stu-id="b2e10-170">Client-to-server streaming</span></span>

<span data-ttu-id="b2e10-171">I client JavaScript chiamano i metodi di streaming da client a server negli hub passando un `Subject` come argomento a `send` , `invoke` o `stream` , a seconda del metodo dell'hub richiamato.</span><span class="sxs-lookup"><span data-stu-id="b2e10-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="b2e10-172">`Subject`È una classe che ha un aspetto simile a `Subject` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="b2e10-173">Ad esempio, in RxJS è possibile usare la classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) di tale libreria.</span><span class="sxs-lookup"><span data-stu-id="b2e10-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="b2e10-174">La chiamata `subject.next(item)` a con un elemento scrive l'elemento nel flusso e il metodo Hub riceve l'elemento nel server.</span><span class="sxs-lookup"><span data-stu-id="b2e10-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="b2e10-175">Per terminare il flusso, chiamare `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="b2e10-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="b2e10-176">Client Java</span><span class="sxs-lookup"><span data-stu-id="b2e10-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="b2e10-177">Streaming da server a client</span><span class="sxs-lookup"><span data-stu-id="b2e10-177">Server-to-client streaming</span></span>

<span data-ttu-id="b2e10-178">Il :::no-loc(SignalR)::: client Java usa il `stream` metodo per richiamare i metodi di streaming.</span><span class="sxs-lookup"><span data-stu-id="b2e10-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="b2e10-179">`stream` accetta tre o più argomenti:</span><span class="sxs-lookup"><span data-stu-id="b2e10-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="b2e10-180">Tipo previsto degli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="b2e10-181">Nome del metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2e10-181">The name of the hub method.</span></span>
* <span data-ttu-id="b2e10-182">Argomenti definiti nel metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b2e10-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="b2e10-183">Il `stream` metodo su `HubConnection` restituisce un oggetto osservabile del tipo di elemento del flusso.</span><span class="sxs-lookup"><span data-stu-id="b2e10-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="b2e10-184">Il metodo del tipo osservabile `subscribe` è `onNext` dove `onError` `onCompleted` sono definiti i gestori e.</span><span class="sxs-lookup"><span data-stu-id="b2e10-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b2e10-185">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b2e10-185">Additional resources</span></span>

* [<span data-ttu-id="b2e10-186">Hub</span><span class="sxs-lookup"><span data-stu-id="b2e10-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b2e10-187">Client .NET</span><span class="sxs-lookup"><span data-stu-id="b2e10-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b2e10-188">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="b2e10-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="b2e10-189">Pubblicare in Azure</span><span class="sxs-lookup"><span data-stu-id="b2e10-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
