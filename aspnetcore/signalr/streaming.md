---
title: Usare il flusso in ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come trasmettere i dati tra il client e il server.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
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
uid: signalr/streaming
ms.openlocfilehash: 2f21248934395b682adf8060dae4e3d145e52215
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058207"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>Usare il flusso in ASP.NET Core SignalR

Di [Brennan Conroy](https://github.com/BrennanConroy)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR supporta lo streaming da client a server e da server a client. Questa operazione è utile per gli scenari in cui i frammenti di dati arrivano nel tempo. Quando si esegue il flusso, ogni frammento viene inviato al client o al server non appena diventa disponibile, anziché attendere che tutti i dati diventino disponibili.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR supporta i valori restituiti di streaming dei metodi del server. Questa operazione è utile per gli scenari in cui i frammenti di dati arrivano nel tempo. Quando un valore restituito viene trasmesso al client, ogni frammento viene inviato al client non appena diventa disponibile, anziché attendere che tutti i dati diventino disponibili.

::: moniker-end

[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Configurare un hub per lo streaming

::: moniker range=">= aspnetcore-3.0"

Un metodo Hub diventa automaticamente un metodo dell'hub di flusso quando restituisce <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , `Task<IAsyncEnumerable<T>>` o `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Un metodo Hub diventa automaticamente un metodo dell'hub di flusso quando restituisce <xref:System.Threading.Channels.ChannelReader%601> o `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming da server a client

::: moniker range=">= aspnetcore-3.0"

I metodi dell'hub di streaming possono restituire `IAsyncEnumerable<T>` oltre a `ChannelReader<T>` . Il modo più semplice per restituire `IAsyncEnumerable<T>` consiste nel rendere il metodo dell'hub un metodo iteratore asincrono come illustrato nell'esempio seguente. I metodi iteratori asincroni dell'hub possono accettare un `CancellationToken` parametro che viene attivato quando il client Annulla la sottoscrizione dal flusso. I metodi iterator asincroni evitano problemi comuni con i canali, ad esempio la mancata restituzione del tempo `ChannelReader` sufficiente o l'uscita dal metodo senza completare <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Nell'esempio seguente vengono illustrate le nozioni di base per il flusso di dati al client tramite canali. Ogni volta che un oggetto viene scritto in <xref:System.Threading.Channels.ChannelWriter%601> , l'oggetto viene immediatamente inviato al client. Alla fine, `ChannelWriter` viene completata per indicare al client che il flusso è chiuso.

> [!NOTE]
> Scrivere in `ChannelWriter<T>` in un thread in background e restituire il appena `ChannelReader` possibile. Altre chiamate all'hub vengono bloccate fino a quando non `ChannelReader` viene restituito un oggetto.
>
> Eseguire il wrapping della logica in un' [ `try ... catch` istruzione](/dotnet/csharp/language-reference/keywords/try-catch). Completare `Channel` in un [ `finally` blocco](/dotnet/csharp/language-reference/keywords/try-catch-finally). Se si vuole propagare un errore, acquisirlo all'interno del `catch` blocco e scriverlo nel `finally` blocco.

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

I metodi dell'hub di streaming da server a client possono accettare un `CancellationToken` parametro che viene attivato quando il client Annulla la sottoscrizione dal flusso. Usare questo token per arrestare l'operazione server e rilasciare tutte le risorse se il client si disconnette prima della fine del flusso.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming da client a server

Un metodo Hub diventa automaticamente un metodo dell'hub di streaming da client a server quando accetta uno o più oggetti di tipo <xref:System.Threading.Channels.ChannelReader%601> o <xref:System.Collections.Generic.IAsyncEnumerable%601> . Nell'esempio seguente vengono illustrate le nozioni di base per la lettura dei dati di streaming inviati dal client. Ogni volta che il client scrive in <xref:System.Threading.Channels.ChannelWriter%601> , i dati vengono scritti in `ChannelReader` sul server da cui viene letto il metodo dell'hub.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>Segue una versione del metodo.

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

## <a name="net-client"></a>Client .NET

### <a name="server-to-client-streaming"></a>Streaming da server a client


::: moniker range=">= aspnetcore-3.0"

I `StreamAsync` `StreamAsChannelAsync` metodi e su `HubConnection` vengono usati per richiamare i metodi di streaming da server a client. Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsync` o `StreamAsChannelAsync` . Il parametro generico in `StreamAsync<T>` e `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming. Un oggetto di tipo `IAsyncEnumerable<T>` o `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.

`StreamAsync`Esempio che restituisce `IAsyncEnumerable<int>` :

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

Un `StreamAsChannelAsync` esempio corrispondente che restituisce `ChannelReader<int>` :

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

Il `StreamAsChannelAsync` metodo su `HubConnection` viene usato per richiamare un metodo di streaming da server a client. Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsChannelAsync` . Il parametro generico in `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming. Un oggetto `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.

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

Il `StreamAsChannelAsync` metodo su `HubConnection` viene usato per richiamare un metodo di streaming da server a client. Passare il nome e gli argomenti del metodo Hub definiti nel metodo Hub a `StreamAsChannelAsync` . Il parametro generico in `StreamAsChannelAsync<T>` specifica il tipo di oggetti restituiti dal metodo di streaming. Un oggetto `ChannelReader<T>` viene restituito dalla chiamata del flusso e rappresenta il flusso nel client.

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

### <a name="client-to-server-streaming"></a>Streaming da client a server

Esistono due modi per richiamare un metodo dell'hub di streaming da client a server dal client .NET. È possibile passare un oggetto `IAsyncEnumerable<T>` o `ChannelReader` come argomento a `SendAsync` , `InvokeAsync` o `StreamAsChannelAsync` , a seconda del metodo dell'hub richiamato.

Ogni volta che i dati vengono scritti nell' `IAsyncEnumerable` `ChannelWriter` oggetto o, il metodo dell'hub sul server riceve un nuovo elemento con i dati del client.

Se si usa un `IAsyncEnumerable` oggetto, il flusso termina dopo che il metodo che restituisce gli elementi del flusso viene chiuso.

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

In alternativa, se si usa un `ChannelWriter` , è necessario completare il canale con `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>Client JavaScript

### <a name="server-to-client-streaming"></a>Streaming da server a client

I client JavaScript chiamano i metodi di streaming da server a client negli hub con `connection.stream` . Il `stream` metodo accetta due argomenti:

* Nome del metodo dell'hub. Nell'esempio seguente il nome del metodo dell'hub è `Counter` .
* Argomenti definiti nel metodo dell'hub. Nell'esempio seguente gli argomenti sono un conteggio per il numero di elementi del flusso da ricevere e il ritardo tra gli elementi del flusso.

`connection.stream` Restituisce un oggetto `IStreamResult` che contiene un `subscribe` metodo. Passare un oggetto `IStreamSubscriber` a `subscribe` e impostare `next` i `error` callback, e `complete` per ricevere notifiche dalla `stream` chiamata.

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Per terminare il flusso dal client, chiamare il `dispose` metodo sull'oggetto `ISubscription` restituito dal `subscribe` metodo. La chiamata a questo metodo provoca l'annullamento del `CancellationToken` parametro del metodo Hub, se ne è stato specificato uno.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Per terminare il flusso dal client, chiamare il `dispose` metodo sull'oggetto `ISubscription` restituito dal `subscribe` metodo.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Streaming da client a server

I client JavaScript chiamano i metodi di streaming da client a server negli hub passando un `Subject` come argomento a `send` , `invoke` o `stream` , a seconda del metodo dell'hub richiamato. `Subject`È una classe che ha un aspetto simile a `Subject` . Ad esempio, in RxJS è possibile usare la classe [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) di tale libreria.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

La chiamata `subject.next(item)` a con un elemento scrive l'elemento nel flusso e il metodo Hub riceve l'elemento nel server.

Per terminare il flusso, chiamare `subject.complete()` .

## <a name="java-client"></a>Client Java

### <a name="server-to-client-streaming"></a>Streaming da server a client

Il SignalR client Java usa il `stream` metodo per richiamare i metodi di streaming. `stream` accetta tre o più argomenti:

* Tipo previsto degli elementi del flusso.
* Nome del metodo dell'hub.
* Argomenti definiti nel metodo dell'hub.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

Il `stream` metodo su `HubConnection` restituisce un oggetto osservabile del tipo di elemento del flusso. Il metodo del tipo osservabile `subscribe` è `onNext` dove `onError` `onCompleted` sono definiti i gestori e.

::: moniker-end

## <a name="additional-resources"></a>Risorse aggiuntive

* [Hub](xref:signalr/hubs)
* [Client .NET](xref:signalr/dotnet-client)
* [Client JavaScript](xref:signalr/javascript-client)
* [Pubblicare in Azure](xref:signalr/publish-to-azure-web-app)
