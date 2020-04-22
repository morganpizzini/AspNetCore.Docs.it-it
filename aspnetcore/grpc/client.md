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
# <a name="call-grpc-services-with-the-net-client"></a>Chiamare servizi gRPC con il client .NET

Una libreria client gRPC .NET è disponibile nel pacchetto [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet. Questo documento spiega come:

* Configurare un client gRPC per chiamare i servizi gRPC.
* Effettuare chiamate gRPC a metodi di streaming unario, streaming server, streaming client e bidirezionale.

## <a name="configure-grpc-client"></a>Configurare il client gRPC

I client gRPC sono tipi di client concreti [generati da * \** file con estensione proto](xref:grpc/basics#generated-c-assets). Il client gRPC concreto dispone di metodi che si traducono nel servizio gRPC nel file * \*proto.*

Un client gRPC viene creato da un canale. Iniziare utilizzando `GrpcChannel.ForAddress` per creare un canale e quindi utilizzare il canale per creare un client gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Un canale rappresenta una connessione di lunga durata a un servizio gRPC. Quando viene creato, un canale viene configurato con le opzioni relative alla chiamata di un servizio. Ad esempio, `HttpClient` l'oggetto utilizzato per effettuare chiamate, la dimensione `GrpcChannelOptions` massima dei `GrpcChannel.ForAddress`messaggi di invio e ricezione e la registrazione può essere specificata e utilizzata con . Per un elenco completo delle opzioni, vedere Opzioni di [configurazione client](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Prestazioni e utilizzo di canali e client:

* La creazione di un canale può essere un'operazione costosa. Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in termini di prestazioni.
* i client gRPC vengono creati con i canali. I client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.
* È possibile creare più client gRPC da un canale, inclusi diversi tipi di client.
* Un canale e i client creati dal canale possono essere utilizzati in modo sicuro da più thread.
* I client creati dal canale possono effettuare più chiamate simultanee.

`GrpcChannel.ForAddress`non è l'unica opzione per la creazione di un client gRPC. Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l'integrazione di [factory client gRPC](xref:grpc/clientfactory). L'integrazione `HttpClientFactory` gRPC con offre un'alternativa centralizzata alla creazione di client gRPC.

> [!NOTE]
> È necessaria una configurazione aggiuntiva per [chiamare servizi gRPC non sicuri con il client .NET.](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)

> [!NOTE]
> La chiamata di gRPC `Grpc.Net.Client` su HTTP/2 con non è attualmente supportata su Xamarin. Stiamo lavorando per migliorare il supporto HTTP/2 in una futura versione di Xamarin. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) e [gRPC-Web](xref:grpc/browser) sono alternative praticabili che funzionano oggi.

## <a name="make-grpc-calls"></a>Effettuare chiamate gRPC

Una chiamata gRPC viene avviata chiamando un metodo sul client. Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.

gRPC ha diversi tipi di metodi. Il modo in cui il client viene utilizzato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato. I tipi di metodo gRPC sono:

* Unaria
* Streaming server
* Streaming client
* Streaming bidirezionale

### <a name="unary-call"></a>Chiamata unaria

Una chiamata unaria inizia con il client che invia un messaggio di richiesta. Al termine del servizio viene restituito un messaggio di risposta.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Ogni metodo di servizio unario nel file * \*.proto* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco. Ad esempio, `GreeterClient` su ci sono `SayHello`due modi di chiamare :

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` chiama il servizio in modo asincrono. Ci si può aspettare.
* `GreeterClient.SayHello`- `Greeter.SayHello` chiama il servizio e i blocchi fino al completamento. Non usare nel codice asincrono.

### <a name="server-streaming-call"></a>Chiamata in streaming server

Una chiamata di streaming server inizia con il client che invia un messaggio di richiesta. `ResponseStream.MoveNext()`legge i messaggi trasmessi dal servizio. La chiamata al flusso `ResponseStream.MoveNext()` `false`del server è completa quando restituisce .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Quando si usa C '8 o versione successiva, la `await foreach` sintassi può essere utilizzata per leggere i messaggi. Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:The extension method reads all messages from the response stream:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Chiamata in streaming client

Una chiamata di streaming client viene *avviata senza* che il client invii un messaggio. Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con . Quando il client ha `RequestStream.CompleteAsync` terminato l'invio dei messaggi, deve essere chiamato per notificare il servizio. La chiamata viene terminata quando il servizio restituisce un messaggio di risposta.

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

### <a name="bi-directional-streaming-call"></a>Chiamata in streaming bidirezionale

Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio. Il cliente può scegliere `RequestStream.WriteAsync`di inviare messaggi con . I messaggi trasmessi dal `ResponseStream.MoveNext()` servizio `ResponseStream.ReadAllAsync()`sono accessibili con o . La chiamata in streaming bidirezionale `ResponseStream` è completa quando non contiene più messaggi.

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

Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi tra loro in qualsiasi momento. La logica client migliore per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.

## <a name="access-grpc-trailers"></a>Accedere ai trailer gRPC

Le chiamate gRPC possono restituire trailer gRPC. i trailer gRPC vengono utilizzati per fornire metadati nome/valore relativi a una chiamata. I trailer forniscono funzionalità simili alle intestazioni HTTP, ma vengono ricevuti alla fine della chiamata.

i trailer gRPC `GetTrailers()`sono accessibili tramite , che restituisce una raccolta di metadati. I trailer vengono restituiti al termine della risposta, pertanto è necessario attendere tutti i messaggi di risposta prima di accedere ai trailer.

Le chiamate in streaming unarie e client devono attendere `ResponseAsync` prima di chiamare: `GetTrailers()`

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

Le chiamate di streaming server e bidirezionale devono `GetTrailers()`terminare in attesa del flusso di risposta prima di chiamare :

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

i rimorchi gRPC sono `RpcException`accessibili anche da . Un servizio può restituire trailer con uno stato gRPC non OK. In questo caso i trailer vengono recuperati dall'eccezione generata dal client gRPC:

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

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
