---
title: Chiamare servizi gRPC con il client .NET
author: jamesnk
description: Informazioni su come chiamare i servizi gRPC con il client gRPC .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 9406c2a34126f3e5cd1406a55c3585e7a28f3dd9
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90593047"
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

### <a name="configure-tls"></a>Configurare TLS

Un client gRPC deve usare la stessa sicurezza a livello di connessione del servizio chiamato. gRPC client Transport Layer Security (TLS) viene configurato quando viene creato il canale gRPC. Un client gRPC genera un errore quando chiama un servizio e la sicurezza a livello di connessione del canale e del servizio non corrispondono.

Per configurare un canale gRPC per l'uso di TLS, assicurarsi che l'indirizzo del server inizi con `https` . Ad esempio, `GrpcChannel.ForAddress("https://localhost:5001")` Usa il protocollo HTTPS. Il canale gRPC negotates automaticamente una connessione protetta da TLS e usa una connessione sicura per effettuare chiamate gRPC.

> [!TIP]
> gRPC supporta l'autenticazione dei certificati client su TLS. Per informazioni sulla configurazione dei certificati client con un canale gRPC, vedere <xref:grpc/authn-and-authz#client-certificate-authentication> .

Per chiamare servizi gRPC non protetti, assicurarsi che l'indirizzo del server inizi con `http` . Ad esempio, `GrpcChannel.ForAddress("http://localhost:5000")` Usa il protocollo http. In .NET Core 3,1 o versioni successive è necessaria una configurazione aggiuntiva per [chiamare i servizi gRPC non protetti con il client .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

### <a name="client-performance"></a>Prestazioni client

Prestazioni e utilizzo del canale e del client:

* La creazione di un canale può essere un'operazione costosa. Il riutilizzo di un canale per le chiamate gRPC offre vantaggi in merito alle prestazioni.
* i client gRPC vengono creati con i canali. i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.
* È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.
* Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.
* I client creati dal canale possono effettuare più chiamate simultanee.

`GrpcChannel.ForAddress` non è l'unica opzione per la creazione di un client gRPC. Se si chiamano i servizi gRPC da un'app ASP.NET Core, prendere in considerazione l' [integrazione di gRPC client Factory](xref:grpc/clientfactory). l'integrazione di gRPC con `HttpClientFactory` offre un'alternativa centralizzata alla creazione di client gRPC.

> [!NOTE]
> La chiamata a gRPC su HTTP/2 con `Grpc.Net.Client` non è attualmente supportata in Novell. Stiamo lavorando per migliorare il supporto HTTP/2 in una versione futura di Novell. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) e [Grpc-Web](xref:grpc/browser) sono valide alternative che funzionano oggi.

## <a name="make-grpc-calls"></a>Effettuare chiamate a gRPC

Una chiamata gRPC viene avviata chiamando un metodo sul client. Il client gRPC gestirà la serializzazione dei messaggi e indirizza la chiamata gRPC al servizio corretto.

gRPC dispone di tipi diversi di metodi. Il modo in cui il client viene usato per effettuare una chiamata gRPC dipende dal tipo di metodo chiamato. I tipi di metodo gRPC sono:

* Unario
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

* `GreeterClient.SayHelloAsync` : chiama il `Greeter.SayHello` servizio in modo asincrono. Può essere atteso.
* `GreeterClient.SayHello` : chiama il `Greeter.SayHello` servizio e blocca fino al completamento. Non usare nel codice asincrono.

### <a name="server-streaming-call"></a>Chiamata di streaming del server

Una chiamata di streaming del server inizia con il client che invia un messaggio di richiesta. `ResponseStream.MoveNext()` legge i messaggi trasmessi dal servizio. La chiamata di streaming del server è completata quando `ResponseStream.MoveNext()` restituisce `false` .

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

Una chiamata di streaming client viene avviata *senza* che il client invii un messaggio. Il client può scegliere di inviare messaggi con `RequestStream.WriteAsync` . Al termine dell'invio dei messaggi da parte del client, `RequestStream.CompleteAsync()` è necessario chiamare per inviare una notifica al servizio. La chiamata viene completata quando il servizio restituisce un messaggio di risposta.

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

Per ottenere prestazioni ottimali e per evitare errori non necessari nel client e nel servizio, provare a completare correttamente le chiamate di streaming bidirezionali. Una chiamata bidirezionale viene completata correttamente quando il server ha terminato la lettura del flusso di richiesta e il client ha terminato la lettura del flusso di risposta. La chiamata di esempio precedente è un esempio di una chiamata bidirezionale che termina normalmente. Nella chiamata al client:

1. Avvia una nuova chiamata di flusso bidirezionale chiamando `EchoClient.Echo` .
2. Crea un'attività in background per leggere i messaggi dal servizio usando `ResponseStream.ReadAllAsync()` .
3. Invia messaggi al server con `RequestStream.WriteAsync` .
4. Notifica al server che ha terminato l'invio di messaggi `RequestStream.CompleteAsync()` .
5. Attende che l'attività in background Legga tutti i messaggi in arrivo.

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
var myValue = trailers.GetValue("my-trailer-name");
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
var myValue = trailers.GetValue("my-trailer-name");
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
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a>Configura scadenza

È consigliabile configurare una scadenza della chiamata gRPC in quanto fornisce un limite massimo per quanto tempo è possibile eseguire una chiamata per. Si interrompe l'esecuzione di servizi non confunzionanti e l'esaurimento delle risorse del server. Le scadenze sono uno strumento utile per la creazione di app affidabili.

Configurare `CallOptions.Deadline` per impostare una scadenza per una chiamata gRPC:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Per altre informazioni, vedere <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
