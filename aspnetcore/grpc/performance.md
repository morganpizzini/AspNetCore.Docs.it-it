---
title: Procedure consigliate per le prestazioni
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
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866189"
---
# <a name="performance-best-practices"></a>Procedure consigliate per le prestazioni

Di [James Newton-King](https://twitter.com/jamesnk)

gRPC è progettato per servizi a prestazioni elevate. Questo documento illustra come ottenere le migliori prestazioni possibili da gRPC.

## <a name="reuse-channel"></a>Riutilizza canale

Quando si effettuano chiamate gRPC è necessario riutilizzare un canale gRPC. Il riutilizzo di un canale consente il multiplexing delle chiamate tramite una connessione HTTP/2 esistente.

Se viene creato un nuovo canale per ogni chiamata gRPC, la quantità di tempo necessaria per il completamento può aumentare in modo significativo. Per ogni chiamata sono necessari più round trip di rete tra il client e il server per creare una connessione HTTP/2:

1. Apertura di un socket
2. Creazione della connessione TCP
3. Negoziazione di TLS
4. Avvio della connessione HTTP/2
5. Creazione della chiamata gRPC

I canali possono essere condivisi e riutilizzati in modo sicuro tra le chiamate a gRPC:

* i client gRPC vengono creati con i canali. i client gRPC sono oggetti leggeri e non devono essere memorizzati nella cache o riutilizzati.
* È possibile creare più client gRPC da un canale, inclusi tipi diversi di client.
* Un canale e i client creati dal canale possono essere usati in modo sicuro da più thread.
* I client creati dal canale possono effettuare più chiamate simultanee.

## <a name="connection-concurrency"></a>Concorrenza di connessione

Le connessioni HTTP/2 in genere presentano un limite al numero [massimo di flussi simultanei (richieste HTTP attive)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) in una connessione in una sola volta. Per impostazione predefinita, la maggior parte dei server imposta questo limite su 100 flussi simultanei.

Un canale gRPC usa una singola connessione HTTP/2 e le chiamate simultanee vengono multiplexate su tale connessione. Quando il numero di chiamate attive raggiunge il limite del flusso di connessione, le chiamate aggiuntive vengono accodate nel client. Le chiamate in coda attendono il completamento delle chiamate attive prima di essere inviate. Le applicazioni con carico elevato, o chiamate gRPC di streaming con esecuzione prolungata, potrebbero riscontrare problemi di prestazioni causati dall'accodamento delle chiamate a causa di questo limite.

::: moniker range=">= aspnetcore-5.0"

In .NET 5 è stata introdotta la `SocketsHttpHandler.EnableMultipleHttp2Connections` Proprietà. Quando è impostato su `true` , le connessioni http/2 aggiuntive vengono create da un canale quando viene raggiunto il limite di flusso simultaneo. Quando `GrpcChannel` viene creato un oggetto, il relativo interno `SocketsHttpHandler` viene configurato automaticamente per creare connessioni http/2 aggiuntive. Se un'app configura il proprio gestore, provare a impostare `EnableMultipleHttp2Connections` su `true` :

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

Esistono un paio di soluzioni alternative per le app .NET Core 3,1:

* Creare canali gRPC distinti per le aree dell'app con carico elevato. Ad esempio, il `Logger` servizio gRPC potrebbe avere un carico elevato. Usare un canale separato per creare il `LoggerClient` nell'app.
* Usare un pool di canali gRPC, ad esempio, creare un elenco di canali gRPC. `Random` viene usato per selezionare un canale dall'elenco ogni volta che è necessario un canale gRPC. L'uso di `Random` distribuisce in modo casuale le chiamate su più connessioni.

> [!IMPORTANT]
> Aumentare il limite massimo di flussi simultanei sul server è un altro modo per risolvere questo problema. In gheppio è configurato con <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> Non è consigliabile aumentare il limite massimo di flussi simultanei. Un numero eccessivo di flussi in una singola connessione HTTP/2 introduce nuovi problemi di prestazioni:
>
> * Contesa di thread tra i flussi che tentano di scrivere nella connessione.
> * La perdita di pacchetti di connessione causa il blocco di tutte le chiamate a livello TCP.

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>Ping keep-alive

È possibile usare i ping keep-alive per tenere attive le connessioni HTTP/2 durante i periodi di inattività. Con una connessione HTTP/2 esistente pronta quando un'attività riprende l'attività consente di eseguire rapidamente le chiamate gRPC iniziali, senza un ritardo causato dalla reimpostazione della connessione.

I ping Keep-Alive sono configurati in <xref:System.Net.Http.SocketsHttpHandler> :

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

Il codice precedente configura un canale che invia un ping keep-alive al server ogni 60 secondi durante i periodi di inattività. Il ping garantisce che il server e gli eventuali proxy in uso non chiudano la connessione a causa di inattività.

::: moniker-end

## <a name="streaming"></a>Streaming

il flusso bidirezionale gRPC può essere usato per sostituire le chiamate gRPC unarie in scenari a prestazioni elevate. Una volta avviato un flusso bidirezionale, lo streaming dei messaggi è più veloce rispetto all'invio di messaggi con più chiamate gRPC unarie. I messaggi trasmessi come dati in una richiesta HTTP/2 esistente e eliminano il sovraccarico della creazione di una nuova richiesta HTTP/2 per ogni chiamata unaria.

Servizio di esempio:

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

Client di esempio:

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

La sostituzione di chiamate unarie con flusso bidirezionale per motivi di prestazioni è una tecnica avanzata e non è appropriata in molte situazioni.

L'uso delle chiamate di streaming è una scelta ottimale nei casi seguenti:

1. È necessaria una velocità effettiva elevata o bassa latenza.
2. gRPC e HTTP/2 sono identificati come un collo di bottiglia delle prestazioni.
3. Un thread di lavoro nel client sta inviando o ricevendo messaggi regolari con un servizio gRPC.

Tenere presente la complessità e le limitazioni aggiuntive dell'utilizzo di chiamate di streaming anziché unario:

1. Un flusso può essere interrotto da un errore del servizio o della connessione. La logica è necessaria per riavviare il flusso se si verifica un errore.
2. `RequestStream.WriteAsync` non è sicuro per il multithread. Solo un messaggio può essere scritto in un flusso alla volta. L'invio di messaggi da più thread su un singolo flusso richiede una coda producer/consumer <xref:System.Threading.Channels.Channel%601> , ad esempio i messaggi Marshall.
3. Un metodo di streaming gRPC è limitato a ricevere un tipo di messaggio e a inviare un tipo di messaggio. Ad esempio, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` riceve `RequestMessage` e invia `ResponseMessage` . Il supporto di protobuf per i messaggi sconosciuti o condizionali `Any` che usano e `oneof` può ovviare a questa limitazione.
