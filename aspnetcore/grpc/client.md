---
title: Chiamare servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 9ebe36cdb17e858fd82216b090e3e89169197101
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406186"
---
# <a name="call-grpc-services-with-the-net-client"></a>Chiamare servizi gRPC con il client .NET

Una libreria client di .NET gRPC è disponibile nel pacchetto NuGet [.NET. client di gRPC](https://www.nuget.org/packages/Grpc.Net.Client) . Questo documento illustra come:

* Configurare un client gRPC per chiamare i servizi gRPC.
* Eseguire chiamate gRPC a metodi unari, streaming server, flusso client e flusso bidirezionale.

## <a name="configure-grpc-client"></a>Configurare il client gRPC

i client gRPC sono tipi di client concreti [generati da file * \* . proto* ](xref:grpc/basics#generated-c-assets). Il client gRPC concreto dispone di metodi che vengono convertiti nel servizio gRPC nel file * \* . proto* .

Un client gRPC viene creato da un canale. Iniziare usando `GrpcChannel.ForAddress` per creare un canale e quindi usare il canale per creare un client gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Un canale rappresenta una connessione di lunga durata a un servizio gRPC. Quando viene creato un canale, questo viene configurato con le opzioni correlate alla chiamata a un servizio. Ad esempio, l'oggetto `HttpClient` utilizzato per effettuare chiamate, la dimensione massima dei messaggi di invio e ricezione e la registrazione possono essere specificati `GrpcChannelOptions` e utilizzati con `GrpcChannel.ForAddress` . Per un elenco completo delle opzioni, vedere [Opzioni di configurazione client](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Prestazioni e utilizzo del canale e del client:

* La creazione di un canale può essere un'operazione costosa. Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in merito alle prestazioni.
* i client gRPC vengono creati con i canali. i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.
* È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.
* Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.
* I client creati dal canale possono effettuare più chiamate simultanee.

`GrpcChannel.ForAddress`non è l'unica opzione per la creazione di un client gRPC. Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l' [integrazione di gRPC client Factory](xref:grpc/clientfactory). l'integrazione di gRPC con `HttpClientFactory` offre un'alternativa centralizzata alla creazione di client gRPC.

> [!NOTE]
> È necessaria una configurazione aggiuntiva per [chiamare i servizi gRPC non protetti con il client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> La chiamata a gRPC su HTTP/2 con `Grpc.Net.Client` non è attualmente supportata in Novell. Stiamo lavorando per migliorare il supporto HTTP/2 in una versione futura di Novell. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) sono valide alternative che funzionano oggi.

## <a name="make-grpc-calls"></a>Effettuare chiamate a gRPC

Una chiamata gRPC viene avviata chiamando un metodo sul client. Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.

gRPC dispone di tipi diversi di metodi. Il modo in cui il client viene usato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato. I tipi di metodo gRPC sono:

* Unaria
* Streaming Server
* Flusso client
* Streaming bidirezionale

### <a name="unary-call"></a>Chiamata unaria

Una chiamata unaria inizia con il client che invia un messaggio di richiesta. Al termine del servizio viene restituito un messaggio di risposta.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Ogni metodo del servizio unario nel file * \* . proto* genererà due metodi .NET sul tipo di client gRPC concreto per chiamare il metodo: un metodo asincrono e un metodo di blocco. Ad esempio, in `GreeterClient` sono disponibili due modi per chiamare `SayHello` :

* `GreeterClient.SayHelloAsync`: chiama il `Greeter.SayHello` servizio in modo asincrono. Può essere atteso.
* `GreeterClient.SayHello`: chiama il `Greeter.SayHello` servizio e blocca fino al completamento. Non usare nel codice asincrono.

### <a name="server-streaming-call"></a>Chiamata di streaming del server

Una chiamata di streaming del server inizia con il client che invia un messaggio di richiesta. `ResponseStream.MoveNext()`legge i messaggi trasmessi dal servizio. La chiamata di streaming del server è completata quando `ResponseStream.MoveNext()` restituisce `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Quando si usa C# 8 o versione successiva, `await foreach` è possibile usare la sintassi per leggere i messaggi. Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso di risposta:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Chiamata di streaming client

Una chiamata di streaming client viene avviata *senza* che il client invii un messaggio. Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` . Al termine dell'invio dei messaggi da parte del client, `RequestStream.CompleteAsync` è necessario chiamare per inviare una notifica al servizio. La chiamata viene completata quando il servizio restituisce un messaggio di risposta.

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

### <a name="bi-directional-streaming-call"></a>Chiamata di streaming bidirezionale

Una chiamata di streaming bidirezionale inizia *senza* che il client invii un messaggio. Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` . I messaggi trasmessi dal servizio sono accessibili con `ResponseStream.MoveNext()` o `ResponseStream.ReadAllAsync()` . La chiamata di streaming bidirezionale è completa quando `ResponseStream` non ha più messaggi.

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

Durante una chiamata di streaming bidirezionale, il client e il servizio possono inviare messaggi l'uno all'altro in qualsiasi momento. La migliore logica client per l'interazione con una chiamata bidirezionale varia a seconda della logica del servizio.

## <a name="access-grpc-trailers"></a>Accedi a gRPC Trailers

le chiamate gRPC possono restituire trailer di gRPC. i trailer gRPC vengono usati per fornire i metadati nome/valore relativi a una chiamata. I trailer forniscono funzionalità simili alle intestazioni HTTP, ma vengono ricevute alla fine della chiamata.

i trailer gRPC sono accessibili tramite `GetTrailers()` , che restituisce una raccolta di metadati. I trailer vengono restituiti al termine della risposta, pertanto è necessario attendere tutti i messaggi di risposta prima di accedere ai trailer.

Prima di chiamare, le chiamate di flusso unario e client devono essere attese `ResponseAsync` `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

Le chiamate di streaming bidirezionali e del server devono terminare in attesa del flusso di risposta prima di chiamare `GetTrailers()` :

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

i trailer gRPC sono accessibili anche da `RpcException` . Un servizio può restituire trailer insieme a uno stato di gRPC non OK. In questa situazione i trailer vengono recuperati dall'eccezione generata dal client gRPC:

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
