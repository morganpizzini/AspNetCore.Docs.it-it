---
title: Configurazione SignalR di ASP.NET Core
author: bradygaster
description: Scopri come configurare SignalR le app ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228140"
---
# <a name="aspnet-core-signalr-configuration"></a>Configurazione di ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>Opzioni di serializzazione JSON/MessagePack

ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html). Ogni protocollo dispone di opzioni di configurazione della serializzazione.

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol.JSON](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) serialization can be configured on the server using the AddJsonProtocol extension method. `AddJsonProtocol`può essere aggiunto dopo `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in . Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) di `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> tale oggetto è un oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di System.Text.Json](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare le maiuscole e le `Startup.ConfigureServices`minuscole dei nomi delle proprietà, anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:

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
> Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.

### <a name="switch-to-newtonsoftjson"></a>Passare a Newtonsoft.Json

Se sono necessarie `Newtonsoft.Json` funzionalità non supportate in `System.Text.Json`, vedere Passare a [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call. Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.

> [!NOTE]
> Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.

## <a name="configure-server-options"></a>Configurare le opzioni del server

Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo. Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione. Il valore consigliato `KeepAliveInterval` è double del valore.|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione. Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub. Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi di flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .

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

Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .

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

Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la pressione arretrata. L'aumento di questo valore consente al server di ricevere messaggi più grandi più rapidamente senza applicare la pressione a rito, ma può aumentare il consumo di memoria. |
| `AuthorizationData` | Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app memorizzati nel buffer del server prima di osservare la retropressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi più grandi più rapidamente senza attendere la pressione a riutilizzo, ma può aumentare il consumo di memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto Polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSockets. |
| `MinimumProtocolVersion` | 0 | Specificare la versione minima del protocollo di negoziazione. Viene utilizzato per limitare i client alle versioni più recenti. |

Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling. La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza. |

Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript). È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo . I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server. Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.

Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package. Chiamare `AddConsole` il metodo di estensione:Call the extension method:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste `configureLogging` un metodo simile. Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché `LogLevel` un valore, è `string` anche possibile fornire un valore che rappresenta un nome di livello di log. Ciò è utile quando si configura la registrazione SignalR `LogLevel` in ambienti in cui non si ha accesso alle costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di registrazione disponibili. Il valore da `configureLogging` specificare per imposta il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo di esso nella tabella.**

| string                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o o**`information` | `LogLevel.Information` |
| `warn`**o o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .

Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).

Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica. Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questo può essere tranquillamente ignorato.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript). Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione di Websockets client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene `withTransport` selezionato `HttpHubConnectionBuilder`con il metodo sul metodo . Per impostazione predefinita, il client Java utilizza il trasporto WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il client Java SignalR non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione del portatore

Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ). Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets). In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.

Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :

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

Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e keep-alive

Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript). Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSockets. Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP. Non utilizzato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza. **Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Un proxy HTTP da utilizzare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets. Ciò consente l'utilizzo dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`

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

La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol.JSON](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) serialization can be configured on the server using the AddJsonProtocol extension method. `AddJsonProtocol`può essere aggiunto dopo `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in . Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto. La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) di `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> tale oggetto è un oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione di System.Text.Json](/dotnet/api/system.text.json).

Ad esempio, per configurare il serializzatore in modo da non modificare le maiuscole e le `Startup.ConfigureServices`minuscole dei nomi delle proprietà, anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in :

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:

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
> Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.

### <a name="switch-to-newtonsoftjson"></a>Passare a Newtonsoft.Json

Se sono necessarie `Newtonsoft.Json` funzionalità non supportate in `System.Text.Json`, vedere Passare a [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call. Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.

> [!NOTE]
> Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.

## <a name="configure-server-options"></a>Configurare le opzioni del server

Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo. Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione. Il valore consigliato `KeepAliveInterval` è double del valore.|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione. Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub. Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate. |
| `StreamBufferCapacity` | `10` | Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client. Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi di flusso.|
| `MaximumReceiveMessageSize` | 32 KB | Dimensione massima di un singolo messaggio dell'hub in ingresso. |

Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .

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

Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .

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

Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la pressione arretrata. L'aumento di questo valore consente al server di ricevere messaggi più grandi più rapidamente senza applicare la pressione a rito, ma può aumentare il consumo di memoria. |
| `AuthorizationData` | Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app memorizzati nel buffer del server prima di osservare la retropressione. L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi più grandi più rapidamente senza attendere la pressione a riutilizzo, ma può aumentare il consumo di memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto Polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSockets. |

Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling. La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza. |

Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript). È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo . I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server. Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.

Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package. Chiamare `AddConsole` il metodo di estensione:Call the extension method:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste `configureLogging` un metodo simile. Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anziché `LogLevel` un valore, è `string` anche possibile fornire un valore che rappresenta un nome di livello di log. Ciò è utile quando si configura la registrazione SignalR `LogLevel` in ambienti in cui non si ha accesso alle costanti.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Nella tabella seguente sono elencati i livelli di registrazione disponibili. Il valore da `configureLogging` specificare per imposta il livello di registrazione **minimo** che verrà registrato. Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo di esso nella tabella.**

| string                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**o o**`information` | `LogLevel.Information` |
| `warn`**o o**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .

Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).

Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica. Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questo può essere tranquillamente ignorato.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript). Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione di Websockets client Java è l'unico trasporto disponibile.

Nel client Java il trasporto viene `withTransport` selezionato `HttpHubConnectionBuilder`con il metodo sul metodo . Per impostazione predefinita, il client Java utilizza il trasporto WebSockets.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Il client Java SignalR non supporta ancora il fallback del trasporto.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione del portatore

Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ). Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets). In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.

Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :

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

Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e keep-alive

Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript). Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSockets. Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP. Non utilizzato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza. **Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Un proxy HTTP da utilizzare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets. Ciò consente l'utilizzo dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`

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

La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo. Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:

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
> Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call. Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.

> [!NOTE]
> Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.

## <a name="configure-server-options"></a>Configurare le opzioni del server

Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 secondi | Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo. Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione. Il valore consigliato `KeepAliveInterval` è double del valore.|
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione. Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub. Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate. |

Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .

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

Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .

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

Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client memorizzato nel buffer del server. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. |
| `AuthorizationData` | Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app memorizzati nel buffer del server. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto Polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSockets. |

Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling. La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza. |

Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript). È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo . I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server. Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.

Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package. Chiamare `AddConsole` il metodo di estensione:Call the extension method:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste `configureLogging` un metodo simile. Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .

Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).

Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica. Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questo può essere tranquillamente ignorato.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript). Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In questa versione di Websockets client Java è l'unico trasporto disponibile.

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione del portatore

Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ). Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets). In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.

Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :

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

Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e keep-alive

Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript). Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `keepAliveIntervalInMilliseconds` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 secondi (15.000 millisecondi) | Determina l'intervallo in cui il client invia messaggi ping. L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo. Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso. |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSockets. Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP. Non utilizzato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza. **Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Un proxy HTTP da utilizzare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets. Ciò consente l'utilizzo dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`

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

La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo. Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto. La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti. Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:

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
> Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.

### <a name="messagepack-serialization-options"></a>Opzioni di serializzazione MessagePack

La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call. Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.

> [!NOTE]
> Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.

## <a name="configure-server-options"></a>Configurare le opzioni del server

Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 5 secondi | Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 5 secondi | Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione. Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client. Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.  |
| `SupportedProtocols` | Tutti i protocolli installati | Protocolli supportati da questo hub. Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub. |
| `EnableDetailedErrors` | `false` | Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub. Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate. |

Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .

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

Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Opzioni di configurazione HTTP avanzate

Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria. Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .

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

Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Numero massimo di byte ricevuti dal client memorizzato nel buffer del server. L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. |
| `AuthorizationData` | Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub. | Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub. |
| `TransportMaxBufferSize` | 32 KB | Numero massimo di byte inviati dall'app memorizzati nel buffer del server. L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria. |
| `Transports` | Tutti i trasporti sono abilitati. | Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi. |
| `LongPolling` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto Polling lungo. |
| `WebSockets` | Vedere di seguito. | Opzioni aggiuntive specifiche per il trasporto WebSockets. |

Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 secondi | Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling. La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza. |

Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:

| Opzione | Default Value | Descrizione |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 secondi | Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata. |
| `SubProtocolSelector` | `null` | Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato. Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato. |

## <a name="configure-client-options"></a>Configurare le opzioni client

Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript). È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.

### <a name="configure-logging"></a>Configurare la registrazione

La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo . I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server. Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.

> [!NOTE]
> Per registrare i provider di registrazione, è necessario installare i pacchetti necessari. Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.

Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package. Chiamare `AddConsole` il metodo di estensione:Call the extension method:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Nel client JavaScript esiste `configureLogging` un metodo simile. Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre. I log vengono scritti nella finestra della console del browser.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .

Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).

Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione. Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica. Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Questo può essere tranquillamente ignorato.

### <a name="configure-allowed-transports"></a>Configurare i trasporti consentiti

I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript). Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati. Tutti i trasporti sono abilitati per impostazione predefinita.

Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Configurare l'autenticazione del portatore

Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato. Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ). Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets). In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.

Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :

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

Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>. Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Configurare le opzioni di timeout e keep-alive

Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript). Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |
| `HandshakeTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript). Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping. |

# <a name="java"></a>[Java](#tab/java)

| Opzione | Valore predefinito | Descrizione |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 secondi (30.000 millisecondi) | Timeout per l'attività del server. Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento. Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout. Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server, per consentire l'arrivo di ping. |
| `withHandshakeResponseTimeout` | 5 secondi | Timeout per l'handshake iniziale del server. Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento. Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave. Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Configurare opzioni aggiuntive

È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:

# <a name="net"></a>[.NET](#tab/dotnet)

| Opzione .NET |  Valore predefinito | Descrizione |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `SkipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `ClientCertificates` | Empty | Raccolta di certificati TLS da inviare per autenticare le richieste. |
| `Cookies` | Empty | Raccolta di cookie HTTP da inviare con ogni richiesta HTTP. |
| `Credentials` | Empty | Credenziali da inviare con ogni richiesta HTTP. |
| `CloseTimeout` | 5 secondi | Solo WebSockets. Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura. Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette. |
| `Headers` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |
| `HttpMessageHandlerFactory` | `null` | Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP. Non utilizzato per le connessioni WebSocket. Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro. Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza. **Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.** |
| `Proxy` | `null` | Un proxy HTTP da utilizzare per l'invio di richieste HTTP. |
| `UseDefaultCredentials` | `false` | Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets. Ciò consente l'utilizzo dell'autenticazione di Windows. |
| `WebSocketConfiguration` | `null` | Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive. Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Opzione JavaScript | Default Value | Descrizione |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `skipNegotiation` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |

# <a name="java"></a>[Java](#tab/java)

| Opzione Java | Default Value | Descrizione |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP. |
| `shouldSkipNegotiate` | `false` | Impostare `true` questa opzione su per ignorare il passaggio di negoziazione. **Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.** Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service. |
| `withHeader` `withHeaders` | Empty | Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP. |

---

Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`

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
