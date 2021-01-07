---
title: Configurazione di ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come configurare ASP.NET Core SignalR app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: cef05b32731e0930d7a3cfc6fe4502236b07a236
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972067"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a>Configurazione di ASP.NET Core SignalR

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` . Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

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

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft.Js

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |
| `MaximumParallelInvocationsPerClient` | 1 | Numero massimo di metodi dell'hub che ogni client può chiamare in parallelo prima della coda. |

È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .

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

Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |
| `MinimumProtocolVersion` | 0 | Specificare la versione minima del protocollo Negotiate. Viene usato per limitare i client a versioni più recenti. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o**`information` | `LogLevel.Information` |
| `warn`**o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).

Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.

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

I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript). Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` . Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il SignalR client Java non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket). In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .

Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Valore predefinito | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione. |
| `headers` | `null` | Dizionario di intestazioni inviate con tutte le richieste HTTP. L'invio di intestazioni nel browser non funziona per WebSocket o per il <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flusso. |
| `logMessageContent` | `null` | Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client. |
| `skipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withCredentials` | `true` | Specifica se le credenziali verranno inviate con la richiesta CORS. App Azure servizio usa cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per il corretto funzionamento. Per ulteriori informazioni su CORS con SignalR , vedere <xref:signalr/security#cross-origin-resource-sharing> . |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Valore predefinito | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` . Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

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

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft.Js

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .

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

Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |
| `MinimumProtocolVersion` | 0 | Specificare la versione minima del protocollo Negotiate. Viene usato per limitare i client a versioni più recenti. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o**`information` | `LogLevel.Information` |
| `warn`**o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).

Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.

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

I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript). Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` . Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il SignalR client Java non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket). In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .

Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Valore predefinito | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione. |
| `logMessageContent` | `null` | Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client. |
| `skipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Valore predefinito | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) . `AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` . Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

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

### <a name="switch-to-newtonsoftjson"></a>Passa a Newtonsoft.Js

Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .

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

Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione. L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log. Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di log disponibili. Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.

| Stringa                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o**`information` | `LogLevel.Information` |
| `warn`**o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).

Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.

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

I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript). Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` . Per impostazione predefinita, il client Java usa il trasporto WebSocket.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il SignalR client Java non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket). In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .

Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Valore predefinito | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione. |
| `logMessageContent` | `null` | Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client. |
| `skipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Valore predefinito | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

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

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo. Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione. Il valore consigliato è Double `KeepAliveInterval` .|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate. |

È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .

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

Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).

Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.

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

I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript). Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione dei WebSocket client Java è l'unico trasporto disponibile.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket). In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .

Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in corrispondenza del quale il client invia messaggi ping. L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Valore predefinito | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione. |
| `logMessageContent` | `null` | Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client. |
| `skipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Valore predefinito | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo. Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). `Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:

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

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) . Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.

## <a name="configure-server-options"></a>Configurare le opzioni del server

La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione. Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub. |
| `EnableDetailedErrors` | `false` | Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub. Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate. |

È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .

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

Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `AuthorizationData` | Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app che il server memorizza nel buffer. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione. |
| `LongPolling` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto di polling lungo. |
| `WebSockets` | Vedere qui di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSocket. |

Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling. La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling. |

Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript. È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo. I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server. Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.

Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet. Chiamare il `AddConsole` metodo di estensione:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste un metodo simile `configureLogging` . Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.

Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).

Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica. Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.

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

I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript). Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione della porta

Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ). Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket). In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .

Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java). Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava. Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e Keep-Alive

Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript). Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento. Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout. Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` , per consentire l'arrivo dei ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSocket. Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP. Non usato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza. **Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Proxy HTTP da usare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket. Questo consente l'uso dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere usato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Valore predefinito | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione. |
| `logMessageContent` | `null` | Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client. |
| `skipNegotiation` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Valore predefinito | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**. Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR . |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
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
