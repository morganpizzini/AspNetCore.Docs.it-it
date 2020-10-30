---
title: Creare i servizi e i metodi di gRPC
author: jamesnk
description: Informazioni su come creare i servizi e i metodi gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: cc9fc50871cbad1f2ddf63d3c13c3253f24a995b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058740"
---
# <a name="create-grpc-services-and-methods"></a>Creare i servizi e i metodi di gRPC

Di [James Newton-King](https://twitter.com/jamesnk)

Questo documento illustra come creare i servizi e i metodi di gRPC in C#. Gli argomenti includono:

* Come definire i servizi e i metodi nei file *. proto* .
* Codice generato usando gli strumenti C# di gRPC.
* Implementazione dei servizi e dei metodi di gRPC.

## <a name="create-new-grpc-services"></a>Creare nuovi servizi gRPC

i [Servizi gRPC con C#](xref:grpc/basics) hanno introdotto l'approccio di primo contratto di gRPC per lo sviluppo di API. I servizi e i messaggi vengono definiti nei file *. proto* . Gli strumenti C# generano quindi il codice dai file *. proto* . Per gli asset lato server, viene generato un tipo di base astratto per ogni servizio, insieme alle classi per qualsiasi messaggio.

Il file con *estensione proto* seguente:

* Definisce un `Greeter` servizio.
* Il `Greeter` servizio definisce una `SayHello` chiamata.
* `SayHello` Invia un `HelloRequest` messaggio e riceve un `HelloReply` messaggio

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Gli strumenti c# generano il `GreeterBase` tipo di base c#:

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

Per impostazione predefinita, l'oggetto generato `GreeterBase` non esegue alcuna operazione. Il `SayHello` metodo virtuale restituirà un `UNIMPLEMENTED` errore a tutti i client che lo chiamano. Affinché il servizio sia utile, un'app deve creare un'implementazione concreta di `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

L'implementazione del servizio viene registrata con l'app. Se il servizio è ospitato da ASP.NET Core gRPC, è necessario aggiungerlo alla pipeline di routing con il `MapGrpcService` metodo.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Per altre informazioni, vedere <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Implementare metodi gRPC

Un servizio gRPC può disporre di tipi diversi di metodi. Il modo in cui i messaggi vengono inviati e ricevuti da un servizio dipende dal tipo di metodo definito. I tipi di metodo gRPC sono:

* Unario
* Streaming Server
* Flusso client
* Streaming bidirezionale

Le chiamate di streaming vengono specificate con la `stream` parola chiave nel file *. proto* . `stream` può essere inserito in un messaggio di richiesta di chiamata, in un messaggio di risposta o in entrambi.

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

Ogni tipo di chiamata ha una firma del metodo diversa. Eseguendo l'override dei metodi generati dal tipo di servizio di base astratto in un'implementazione concreta vengono utilizzati gli argomenti corretti e il tipo restituito.

### <a name="unary-method"></a>Metodo unario

Un metodo unario ottiene il messaggio di richiesta come parametro e restituisce la risposta. Una chiamata unaria viene completata quando viene restituita la risposta.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Le chiamate unarie sono le più simili alle [azioni sui controller API Web](xref:web-api/index). Un'importante differenza tra i metodi gRPC delle azioni è che i metodi gRPC non sono in grado di associare parti di una richiesta a diversi argomenti del metodo. i metodi gRPC hanno sempre un argomento di messaggio per i dati della richiesta in ingresso. È comunque possibile inviare più valori a un servizio gRPC rendendoli i campi nel messaggio di richiesta:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Metodo di streaming del server

Un metodo di streaming del server ottiene il messaggio di richiesta come parametro. Poiché è possibile trasmettere più messaggi al chiamante, `responseStream.WriteAsync` viene usato per inviare messaggi di risposta. Una chiamata di streaming del server è completa quando il metodo restituisce.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

Il client non è in grado di inviare messaggi o dati aggiuntivi dopo l'avvio del metodo di streaming del server. Alcuni metodi di streaming sono progettati per essere eseguiti per sempre. Per i metodi di streaming continuo, un client può annullare la chiamata quando non è più necessaria. Quando si verifica l'annullamento, il client invia un segnale al server e viene generato [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . Il `CancellationToken` token deve essere usato nel server con metodi asincroni, in modo che:

* Tutte le operazioni asincrone vengono annullate insieme alla chiamata di streaming.
* Il metodo si chiude rapidamente.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a>Metodo di streaming client

Un metodo di flusso client viene avviato *senza* il metodo che riceve un messaggio. Il `requestStream` parametro viene utilizzato per leggere i messaggi dal client. Una chiamata di flusso client viene completata quando viene restituito un messaggio di risposta:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

Quando si usa C# 8 o versione successiva, `await foreach` è possibile usare la sintassi per leggere i messaggi. Il `IAsyncStreamReader<T>.ReadAllAsync()` metodo di estensione legge tutti i messaggi dal flusso della richiesta:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a>Metodo di streaming bidirezionale

Un metodo di streaming bidirezionale inizia *senza* il metodo che riceve un messaggio. Il `requestStream` parametro viene utilizzato per leggere i messaggi dal client. Il metodo può scegliere di inviare messaggi con `responseStream.WriteAsync` . Una chiamata di flusso bidirezionale è completa quando il metodo restituisce:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

Il codice precedente:

* Invia una risposta per ogni richiesta.
* È un utilizzo di base dello streaming bidirezionale.

È possibile supportare scenari più complessi, ad esempio la lettura di richieste e l'invio simultaneo di risposte:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

In un metodo di streaming bidirezionale, il client e il servizio possono inviare messaggi l'uno all'altro in qualsiasi momento. La migliore implementazione di un metodo bidirezionale varia a seconda dei requisiti.

## <a name="access-grpc-request-headers"></a>Accedi alle intestazioni delle richieste gRPC

Un messaggio di richiesta non è l'unico modo per un client di inviare dati a un servizio gRPC. I valori di intestazione sono disponibili in un servizio utilizzando `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:grpc/basics>
* <xref:grpc/client>
