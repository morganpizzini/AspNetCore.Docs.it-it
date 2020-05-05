---
title: Configurazione SignalR di ASP.NET Core
author: bradygaster
description: Informazioni su come configurare ASP.NET Core SignalR app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 054462c37fffd1973cbbe4f76ae4a3be5a6c1778
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767304"
---
# <a name="aspnet-core-signalr-configuration"></a>Configurazione di ASP.NET Core SignalR

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft. JSON

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client. Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |
| `MinimumProtocolVersion` | 0 | Specificare la versione minima del protocollo Negotiate. Viene usato per limitare i client a versioni più recenti. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **o** `information` | `LogLevel.Information` |
| `warn` **o** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.

Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript). Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder` Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il client Java SignalR non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket). In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.

Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `ClientCertificates` | Vuoto | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Vuoto | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Vuoto | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withCredentials` | `true` | Specifica se le credenziali verranno inviate con la richiesta CORS. App Azure servizio usa i cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per funzionare correttamente. Per ulteriori informazioni su CORS con SignalR, vedere <xref:signalr/security#cross-origin-resource-sharing>. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withHeader` `withHeaders` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft. JSON

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client. Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |
| `MinimumProtocolVersion` | 0 | Specificare la versione minima del protocollo Negotiate. Viene usato per limitare i client a versioni più recenti. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Default Value | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Default Value | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **o** `information` | `LogLevel.Information` |
| `warn` **o** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.

Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript). Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder` Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il client Java SignalR non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket). In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.

Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `ClientCertificates` | Vuoto | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Vuoto | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Vuoto | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Description |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Description |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withHeader` `withHeaders` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft. JSON

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client. Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info` **o** `information` | `LogLevel.Information` |
| `warn` **o** `warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.

Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript). Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder` Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il client Java SignalR non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket). In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.

Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `ClientCertificates` | Vuoto | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Vuoto | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Vuoto | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withHeader` `withHeaders` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto `Startup.ConfigureServices` dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un oggetto `JsonSerializerSettings` JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il `Startup.ConfigureServices`codice seguente in:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client. Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate. |

È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.

Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript). Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket). In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.

Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `ClientCertificates` | Vuoto | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Vuoto | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Vuoto | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withHeader` `withHeaders` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto `Startup.ConfigureServices` dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un oggetto `JsonSerializerSettings` JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il `Startup.ConfigureServices`codice seguente in:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client. Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate. |

È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in. `Startup.Configure`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Default Value | Description |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Default Value | Description |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.

Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).

Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questa operazione può essere ignorata in modo sicuro.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript). Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket). In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.

Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server, per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `ClientCertificates` | Vuoto | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Vuoto | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Vuoto | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR. |
| `withHeader` `withHeaders` | Vuoto | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Risorse aggiuntive

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
