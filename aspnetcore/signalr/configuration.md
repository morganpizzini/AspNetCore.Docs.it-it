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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="a4b89-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="a4b89-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4b89-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4b89-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4b89-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4b89-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4b89-108">`AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4b89-109">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4b89-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4b89-111">Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4b89-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4b89-112">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:</span><span class="sxs-lookup"><span data-stu-id="a4b89-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a4b89-113">Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4b89-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4b89-114">Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="a4b89-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4b89-115">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4b89-116">Passa a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4b89-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4b89-117">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4b89-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4b89-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-118">MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4b89-120">Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-121">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4b89-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="a4b89-122">Configure server options</span></span>

<span data-ttu-id="a4b89-123">La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4b89-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-124">Option</span></span> | <span data-ttu-id="a4b89-125">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-125">Default Value</span></span> | <span data-ttu-id="a4b89-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4b89-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-127">30 seconds</span></span> | <span data-ttu-id="a4b89-128">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4b89-129">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4b89-130">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4b89-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-131">15 seconds</span></span> | <span data-ttu-id="a4b89-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="a4b89-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4b89-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-134">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-135">15 seconds</span></span> | <span data-ttu-id="a4b89-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4b89-137">Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4b89-138">Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4b89-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="a4b89-139">All installed protocols</span></span> | <span data-ttu-id="a4b89-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-140">Protocols supported by this hub.</span></span> <span data-ttu-id="a4b89-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4b89-142">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4b89-143">Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4b89-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4b89-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4b89-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-146">32 KB</span></span> | <span data-ttu-id="a4b89-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4b89-148">È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4b89-149">Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="a4b89-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4b89-150">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="a4b89-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4b89-151">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4b89-152">Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4b89-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4b89-153">La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4b89-154">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-154">Option</span></span> | <span data-ttu-id="a4b89-155">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-155">Default Value</span></span> | <span data-ttu-id="a4b89-156">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4b89-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-157">32 KB</span></span> | <span data-ttu-id="a4b89-158">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4b89-159">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4b89-160">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4b89-161">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4b89-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-162">32 KB</span></span> | <span data-ttu-id="a4b89-163">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4b89-164">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4b89-165">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-165">All Transports are enabled.</span></span> | <span data-ttu-id="a4b89-166">Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4b89-167">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-167">See below.</span></span> | <span data-ttu-id="a4b89-168">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4b89-169">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-169">See below.</span></span> | <span data-ttu-id="a4b89-170">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a4b89-171">0</span><span class="sxs-lookup"><span data-stu-id="a4b89-171">0</span></span> | <span data-ttu-id="a4b89-172">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a4b89-173">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a4b89-174">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4b89-175">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-175">Option</span></span> | <span data-ttu-id="a4b89-176">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-176">Default Value</span></span> | <span data-ttu-id="a4b89-177">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4b89-178">90 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-178">90 seconds</span></span> | <span data-ttu-id="a4b89-179">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4b89-180">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4b89-181">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4b89-182">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-182">Option</span></span> | <span data-ttu-id="a4b89-183">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-183">Default Value</span></span> | <span data-ttu-id="a4b89-184">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4b89-185">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-185">5 seconds</span></span> | <span data-ttu-id="a4b89-186">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="a4b89-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4b89-187">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4b89-188">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4b89-189">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="a4b89-189">Configure client options</span></span>

<span data-ttu-id="a4b89-190">Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4b89-191">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.</span><span class="sxs-lookup"><span data-stu-id="a4b89-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4b89-192">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="a4b89-192">Configure logging</span></span>

<span data-ttu-id="a4b89-193">La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4b89-194">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4b89-195">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-196">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="a4b89-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4b89-197">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="a4b89-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4b89-198">Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4b89-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4b89-199">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a4b89-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4b89-200">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4b89-201">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="a4b89-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4b89-202">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="a4b89-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4b89-203">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="a4b89-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4b89-204">Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4b89-205">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="a4b89-205">The following table lists the available log levels.</span></span> <span data-ttu-id="a4b89-206">Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4b89-207">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4b89-208">Stringa</span><span class="sxs-lookup"><span data-stu-id="a4b89-208">String</span></span>                      | <span data-ttu-id="a4b89-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4b89-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4b89-210">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="a4b89-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4b89-211">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4b89-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4b89-212">Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4b89-213">Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4b89-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4b89-214">Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4b89-215">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a4b89-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4b89-216">Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4b89-217">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4b89-218">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4b89-219">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="a4b89-219">Configure allowed transports</span></span>

<span data-ttu-id="a4b89-220">I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4b89-221">Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4b89-222">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a4b89-222">All transports are enabled by default.</span></span>

<span data-ttu-id="a4b89-223">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="a4b89-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4b89-224">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4b89-225">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="a4b89-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4b89-226">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a4b89-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4b89-227">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4b89-228">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4b89-229">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="a4b89-229">Configure bearer authentication</span></span>

<span data-ttu-id="a4b89-230">Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4b89-231">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="a4b89-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4b89-232">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4b89-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4b89-233">In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a4b89-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4b89-234">Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4b89-235">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4b89-236">Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4b89-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4b89-237">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4b89-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4b89-238">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4b89-239">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a4b89-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4b89-240">Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-241">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-242">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-242">Option</span></span> | <span data-ttu-id="a4b89-243">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-243">Default value</span></span> | <span data-ttu-id="a4b89-244">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4b89-245">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-246">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-246">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-247">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-248">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-249">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-250">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-250">15 seconds</span></span> | <span data-ttu-id="a4b89-251">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-252">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-253">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-254">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-255">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-255">15 seconds</span></span> | <span data-ttu-id="a4b89-256">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-257">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-258">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4b89-259">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="a4b89-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4b89-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-261">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-261">Option</span></span> | <span data-ttu-id="a4b89-262">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-262">Default value</span></span> | <span data-ttu-id="a4b89-263">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4b89-264">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-265">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-265">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-266">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4b89-267">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-268">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4b89-269">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-270">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-271">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-272">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-273">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-273">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-274">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-274">Option</span></span> | <span data-ttu-id="a4b89-275">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-275">Default value</span></span> | <span data-ttu-id="a4b89-276">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4b89-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4b89-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4b89-278">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-279">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-279">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-280">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-281">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-282">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4b89-283">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-283">15 seconds</span></span> | <span data-ttu-id="a4b89-284">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-285">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-286">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-287">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4b89-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4b89-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4b89-289">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-290">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-291">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-292">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4b89-293">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-293">Configure additional options</span></span>

<span data-ttu-id="a4b89-294">Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="a4b89-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-295">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-296">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-296">.NET Option</span></span> |  <span data-ttu-id="a4b89-297">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-297">Default value</span></span> | <span data-ttu-id="a4b89-298">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4b89-299">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4b89-300">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-301">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-302">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4b89-303">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-303">Empty</span></span> | <span data-ttu-id="a4b89-304">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="a4b89-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4b89-305">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-305">Empty</span></span> | <span data-ttu-id="a4b89-306">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4b89-307">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-307">Empty</span></span> | <span data-ttu-id="a4b89-308">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4b89-309">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-309">5 seconds</span></span> | <span data-ttu-id="a4b89-310">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-310">WebSockets only.</span></span> <span data-ttu-id="a4b89-311">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="a4b89-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4b89-312">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="a4b89-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4b89-313">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-313">Empty</span></span> | <span data-ttu-id="a4b89-314">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4b89-315">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4b89-316">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4b89-317">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4b89-318">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="a4b89-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4b89-319">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="a4b89-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4b89-320">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4b89-321">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4b89-322">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="a4b89-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4b89-323">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a4b89-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4b89-324">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="a4b89-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4b89-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-326">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-326">JavaScript Option</span></span> | <span data-ttu-id="a4b89-327">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-327">Default Value</span></span> | <span data-ttu-id="a4b89-328">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4b89-329">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4b89-330">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-331">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-332">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="a4b89-333">Specifica se le credenziali verranno inviate con la richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="a4b89-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="a4b89-334">App Azure servizio usa i cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per funzionare correttamente.</span><span class="sxs-lookup"><span data-stu-id="a4b89-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="a4b89-335">Per ulteriori informazioni su CORS con SignalR, vedere <xref:signalr/security#cross-origin-resource-sharing>.</span><span class="sxs-lookup"><span data-stu-id="a4b89-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-336">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-336">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-337">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-337">Java Option</span></span> | <span data-ttu-id="a4b89-338">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-338">Default Value</span></span> | <span data-ttu-id="a4b89-339">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4b89-340">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4b89-341">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-342">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-343">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4b89-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4b89-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4b89-345">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-345">Empty</span></span> | <span data-ttu-id="a4b89-346">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4b89-347">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4b89-348">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4b89-349">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4b89-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4b89-350">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4b89-351">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-352">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4b89-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4b89-353">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4b89-354">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4b89-355">`AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4b89-356">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4b89-357">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4b89-358">Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4b89-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4b89-359">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:</span><span class="sxs-lookup"><span data-stu-id="a4b89-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="a4b89-360">Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4b89-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4b89-361">Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="a4b89-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4b89-362">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4b89-363">Passa a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4b89-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4b89-364">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4b89-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4b89-365">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-365">MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-366">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4b89-367">Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-368">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4b89-369">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="a4b89-369">Configure server options</span></span>

<span data-ttu-id="a4b89-370">La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4b89-371">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-371">Option</span></span> | <span data-ttu-id="a4b89-372">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-372">Default Value</span></span> | <span data-ttu-id="a4b89-373">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4b89-374">30 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-374">30 seconds</span></span> | <span data-ttu-id="a4b89-375">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4b89-376">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4b89-377">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4b89-378">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-378">15 seconds</span></span> | <span data-ttu-id="a4b89-379">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="a4b89-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4b89-380">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-381">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-382">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-382">15 seconds</span></span> | <span data-ttu-id="a4b89-383">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4b89-384">Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4b89-385">Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4b89-386">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="a4b89-386">All installed protocols</span></span> | <span data-ttu-id="a4b89-387">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-387">Protocols supported by this hub.</span></span> <span data-ttu-id="a4b89-388">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4b89-389">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4b89-390">Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4b89-391">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4b89-392">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4b89-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-393">32 KB</span></span> | <span data-ttu-id="a4b89-394">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4b89-395">È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4b89-396">Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="a4b89-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4b89-397">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="a4b89-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4b89-398">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4b89-399">Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4b89-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4b89-400">La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4b89-401">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-401">Option</span></span> | <span data-ttu-id="a4b89-402">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-402">Default Value</span></span> | <span data-ttu-id="a4b89-403">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4b89-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-404">32 KB</span></span> | <span data-ttu-id="a4b89-405">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4b89-406">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4b89-407">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4b89-408">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4b89-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-409">32 KB</span></span> | <span data-ttu-id="a4b89-410">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4b89-411">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4b89-412">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-412">All Transports are enabled.</span></span> | <span data-ttu-id="a4b89-413">Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4b89-414">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-414">See below.</span></span> | <span data-ttu-id="a4b89-415">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4b89-416">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-416">See below.</span></span> | <span data-ttu-id="a4b89-417">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="a4b89-418">0</span><span class="sxs-lookup"><span data-stu-id="a4b89-418">0</span></span> | <span data-ttu-id="a4b89-419">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="a4b89-420">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="a4b89-421">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4b89-422">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-422">Option</span></span> | <span data-ttu-id="a4b89-423">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-423">Default Value</span></span> | <span data-ttu-id="a4b89-424">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4b89-425">90 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-425">90 seconds</span></span> | <span data-ttu-id="a4b89-426">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4b89-427">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4b89-428">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4b89-429">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-429">Option</span></span> | <span data-ttu-id="a4b89-430">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-430">Default Value</span></span> | <span data-ttu-id="a4b89-431">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4b89-432">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-432">5 seconds</span></span> | <span data-ttu-id="a4b89-433">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="a4b89-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4b89-434">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4b89-435">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4b89-436">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="a4b89-436">Configure client options</span></span>

<span data-ttu-id="a4b89-437">Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4b89-438">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.</span><span class="sxs-lookup"><span data-stu-id="a4b89-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4b89-439">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="a4b89-439">Configure logging</span></span>

<span data-ttu-id="a4b89-440">La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4b89-441">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4b89-442">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-443">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="a4b89-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4b89-444">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="a4b89-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4b89-445">Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4b89-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4b89-446">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a4b89-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4b89-447">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4b89-448">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="a4b89-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4b89-449">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="a4b89-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4b89-450">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="a4b89-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4b89-451">Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4b89-452">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="a4b89-452">The following table lists the available log levels.</span></span> <span data-ttu-id="a4b89-453">Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4b89-454">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4b89-455">Stringa</span><span class="sxs-lookup"><span data-stu-id="a4b89-455">String</span></span>                      | <span data-ttu-id="a4b89-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4b89-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4b89-457">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="a4b89-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4b89-458">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4b89-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4b89-459">Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4b89-460">Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4b89-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4b89-461">Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4b89-462">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a4b89-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4b89-463">Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4b89-464">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4b89-465">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4b89-466">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="a4b89-466">Configure allowed transports</span></span>

<span data-ttu-id="a4b89-467">I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4b89-468">Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4b89-469">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a4b89-469">All transports are enabled by default.</span></span>

<span data-ttu-id="a4b89-470">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="a4b89-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4b89-471">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4b89-472">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="a4b89-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4b89-473">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a4b89-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4b89-474">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4b89-475">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4b89-476">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="a4b89-476">Configure bearer authentication</span></span>

<span data-ttu-id="a4b89-477">Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4b89-478">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="a4b89-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4b89-479">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4b89-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4b89-480">In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a4b89-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4b89-481">Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4b89-482">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4b89-483">Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4b89-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4b89-484">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4b89-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4b89-485">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4b89-486">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a4b89-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4b89-487">Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-488">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-489">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-489">Option</span></span> | <span data-ttu-id="a4b89-490">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-490">Default value</span></span> | <span data-ttu-id="a4b89-491">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4b89-492">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-493">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-493">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-494">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-495">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-496">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-497">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-497">15 seconds</span></span> | <span data-ttu-id="a4b89-498">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-499">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-500">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-501">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-502">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-502">15 seconds</span></span> | <span data-ttu-id="a4b89-503">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-504">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-505">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4b89-506">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="a4b89-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4b89-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-508">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-508">Option</span></span> | <span data-ttu-id="a4b89-509">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-509">Default value</span></span> | <span data-ttu-id="a4b89-510">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4b89-511">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-512">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-512">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-513">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4b89-514">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-515">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4b89-516">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-517">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-518">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-519">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-520">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-520">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-521">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-521">Option</span></span> | <span data-ttu-id="a4b89-522">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-522">Default value</span></span> | <span data-ttu-id="a4b89-523">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4b89-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4b89-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4b89-525">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-526">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-526">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-527">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-528">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-529">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4b89-530">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-530">15 seconds</span></span> | <span data-ttu-id="a4b89-531">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-532">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-533">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-534">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4b89-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4b89-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4b89-536">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-537">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-538">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-539">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4b89-540">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-540">Configure additional options</span></span>

<span data-ttu-id="a4b89-541">Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="a4b89-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-542">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-543">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-543">.NET Option</span></span> |  <span data-ttu-id="a4b89-544">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-544">Default value</span></span> | <span data-ttu-id="a4b89-545">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4b89-546">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4b89-547">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-548">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-549">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4b89-550">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-550">Empty</span></span> | <span data-ttu-id="a4b89-551">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="a4b89-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4b89-552">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-552">Empty</span></span> | <span data-ttu-id="a4b89-553">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4b89-554">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-554">Empty</span></span> | <span data-ttu-id="a4b89-555">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4b89-556">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-556">5 seconds</span></span> | <span data-ttu-id="a4b89-557">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-557">WebSockets only.</span></span> <span data-ttu-id="a4b89-558">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="a4b89-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4b89-559">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="a4b89-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4b89-560">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-560">Empty</span></span> | <span data-ttu-id="a4b89-561">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4b89-562">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4b89-563">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4b89-564">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4b89-565">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="a4b89-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4b89-566">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="a4b89-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4b89-567">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4b89-568">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4b89-569">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="a4b89-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4b89-570">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a4b89-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4b89-571">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="a4b89-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4b89-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-573">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-573">JavaScript Option</span></span> | <span data-ttu-id="a4b89-574">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-574">Default Value</span></span> | <span data-ttu-id="a4b89-575">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4b89-576">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4b89-577">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-578">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-579">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-580">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-580">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-581">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-581">Java Option</span></span> | <span data-ttu-id="a4b89-582">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-582">Default Value</span></span> | <span data-ttu-id="a4b89-583">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4b89-584">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4b89-585">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-586">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-587">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4b89-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4b89-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4b89-589">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-589">Empty</span></span> | <span data-ttu-id="a4b89-590">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4b89-591">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4b89-592">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4b89-593">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4b89-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4b89-594">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4b89-595">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-596">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4b89-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4b89-597">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4b89-598">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="a4b89-599">`AddJsonProtocol`può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4b89-600">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4b89-601">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4b89-602">Per ulteriori informazioni, vedere la [documentazione di System. Text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="a4b89-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="a4b89-603">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti " `Startup.ConfigureServices`CamelCase", usare il codice seguente in:</span><span class="sxs-lookup"><span data-stu-id="a4b89-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="a4b89-604">Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4b89-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4b89-605">Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="a4b89-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4b89-606">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="a4b89-607">Passa a Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="a4b89-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="a4b89-608">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate `System.Text.Json`in, vedere [passare a Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="a4b89-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4b89-609">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-609">MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-610">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4b89-611">Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-612">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4b89-613">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="a4b89-613">Configure server options</span></span>

<span data-ttu-id="a4b89-614">La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4b89-615">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-615">Option</span></span> | <span data-ttu-id="a4b89-616">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-616">Default Value</span></span> | <span data-ttu-id="a4b89-617">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4b89-618">30 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-618">30 seconds</span></span> | <span data-ttu-id="a4b89-619">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4b89-620">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4b89-621">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4b89-622">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-622">15 seconds</span></span> | <span data-ttu-id="a4b89-623">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="a4b89-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4b89-624">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-625">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-626">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-626">15 seconds</span></span> | <span data-ttu-id="a4b89-627">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4b89-628">Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4b89-629">Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4b89-630">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="a4b89-630">All installed protocols</span></span> | <span data-ttu-id="a4b89-631">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-631">Protocols supported by this hub.</span></span> <span data-ttu-id="a4b89-632">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4b89-633">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4b89-634">Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="a4b89-635">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="a4b89-636">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="a4b89-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-637">32 KB</span></span> | <span data-ttu-id="a4b89-638">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="a4b89-639">È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4b89-640">Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="a4b89-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4b89-641">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="a4b89-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4b89-642">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4b89-643">Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4b89-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4b89-644">La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4b89-645">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-645">Option</span></span> | <span data-ttu-id="a4b89-646">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-646">Default Value</span></span> | <span data-ttu-id="a4b89-647">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4b89-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-648">32 KB</span></span> | <span data-ttu-id="a4b89-649">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="a4b89-650">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4b89-651">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4b89-652">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4b89-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-653">32 KB</span></span> | <span data-ttu-id="a4b89-654">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="a4b89-655">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4b89-656">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-656">All Transports are enabled.</span></span> | <span data-ttu-id="a4b89-657">Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4b89-658">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-658">See below.</span></span> | <span data-ttu-id="a4b89-659">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4b89-660">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-660">See below.</span></span> | <span data-ttu-id="a4b89-661">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4b89-662">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4b89-663">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-663">Option</span></span> | <span data-ttu-id="a4b89-664">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-664">Default Value</span></span> | <span data-ttu-id="a4b89-665">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4b89-666">90 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-666">90 seconds</span></span> | <span data-ttu-id="a4b89-667">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4b89-668">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4b89-669">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4b89-670">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-670">Option</span></span> | <span data-ttu-id="a4b89-671">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-671">Default Value</span></span> | <span data-ttu-id="a4b89-672">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4b89-673">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-673">5 seconds</span></span> | <span data-ttu-id="a4b89-674">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="a4b89-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4b89-675">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4b89-676">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4b89-677">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="a4b89-677">Configure client options</span></span>

<span data-ttu-id="a4b89-678">Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4b89-679">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.</span><span class="sxs-lookup"><span data-stu-id="a4b89-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4b89-680">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="a4b89-680">Configure logging</span></span>

<span data-ttu-id="a4b89-681">La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4b89-682">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4b89-683">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-684">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="a4b89-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4b89-685">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="a4b89-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4b89-686">Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4b89-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4b89-687">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a4b89-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4b89-688">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4b89-689">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="a4b89-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4b89-690">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="a4b89-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="a4b89-691">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="a4b89-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="a4b89-692">Questa operazione è utile quando si configura la `LogLevel` registrazione di SignalR negli ambienti in cui non è possibile accedere alle costanti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="a4b89-693">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="a4b89-693">The following table lists the available log levels.</span></span> <span data-ttu-id="a4b89-694">Il valore fornito per impostare `configureLogging` il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="a4b89-695">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="a4b89-696">Stringa</span><span class="sxs-lookup"><span data-stu-id="a4b89-696">String</span></span>                      | <span data-ttu-id="a4b89-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="a4b89-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="a4b89-698">`info` **o** `information`</span><span class="sxs-lookup"><span data-stu-id="a4b89-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="a4b89-699">`warn` **o** `warning`</span><span class="sxs-lookup"><span data-stu-id="a4b89-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="a4b89-700">Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4b89-701">Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4b89-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4b89-702">Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4b89-703">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a4b89-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4b89-704">Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4b89-705">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4b89-706">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4b89-707">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="a4b89-707">Configure allowed transports</span></span>

<span data-ttu-id="a4b89-708">I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4b89-709">Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4b89-710">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a4b89-710">All transports are enabled by default.</span></span>

<span data-ttu-id="a4b89-711">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="a4b89-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4b89-712">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4b89-713">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="a4b89-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="a4b89-714">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="a4b89-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="a4b89-715">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4b89-716">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4b89-717">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="a4b89-717">Configure bearer authentication</span></span>

<span data-ttu-id="a4b89-718">Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4b89-719">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="a4b89-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4b89-720">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4b89-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4b89-721">In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a4b89-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4b89-722">Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4b89-723">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4b89-724">Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4b89-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4b89-725">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4b89-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4b89-726">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4b89-727">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a4b89-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4b89-728">Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-729">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-730">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-730">Option</span></span> | <span data-ttu-id="a4b89-731">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-731">Default value</span></span> | <span data-ttu-id="a4b89-732">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4b89-733">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-734">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-734">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-735">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-736">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-737">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-738">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-738">15 seconds</span></span> | <span data-ttu-id="a4b89-739">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-740">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-741">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-742">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-743">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-743">15 seconds</span></span> | <span data-ttu-id="a4b89-744">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-745">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-746">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4b89-747">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="a4b89-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4b89-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-749">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-749">Option</span></span> | <span data-ttu-id="a4b89-750">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-750">Default value</span></span> | <span data-ttu-id="a4b89-751">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4b89-752">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-753">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-753">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-754">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4b89-755">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-756">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4b89-757">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-758">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-759">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-760">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-761">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-761">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-762">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-762">Option</span></span> | <span data-ttu-id="a4b89-763">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-763">Default value</span></span> | <span data-ttu-id="a4b89-764">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4b89-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4b89-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4b89-766">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-767">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-767">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-768">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-769">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-770">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4b89-771">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-771">15 seconds</span></span> | <span data-ttu-id="a4b89-772">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-773">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-774">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-775">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4b89-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4b89-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4b89-777">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-778">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-779">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-780">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4b89-781">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-781">Configure additional options</span></span>

<span data-ttu-id="a4b89-782">Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="a4b89-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-783">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-784">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-784">.NET Option</span></span> |  <span data-ttu-id="a4b89-785">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-785">Default value</span></span> | <span data-ttu-id="a4b89-786">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4b89-787">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4b89-788">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-789">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-790">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4b89-791">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-791">Empty</span></span> | <span data-ttu-id="a4b89-792">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="a4b89-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4b89-793">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-793">Empty</span></span> | <span data-ttu-id="a4b89-794">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4b89-795">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-795">Empty</span></span> | <span data-ttu-id="a4b89-796">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4b89-797">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-797">5 seconds</span></span> | <span data-ttu-id="a4b89-798">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-798">WebSockets only.</span></span> <span data-ttu-id="a4b89-799">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="a4b89-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4b89-800">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="a4b89-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4b89-801">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-801">Empty</span></span> | <span data-ttu-id="a4b89-802">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4b89-803">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4b89-804">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4b89-805">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4b89-806">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="a4b89-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4b89-807">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="a4b89-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4b89-808">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4b89-809">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4b89-810">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="a4b89-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4b89-811">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a4b89-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4b89-812">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="a4b89-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4b89-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-814">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-814">JavaScript Option</span></span> | <span data-ttu-id="a4b89-815">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-815">Default Value</span></span> | <span data-ttu-id="a4b89-816">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4b89-817">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4b89-818">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-819">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-820">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-821">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-821">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-822">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-822">Java Option</span></span> | <span data-ttu-id="a4b89-823">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-823">Default Value</span></span> | <span data-ttu-id="a4b89-824">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4b89-825">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4b89-826">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-827">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-828">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4b89-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4b89-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4b89-830">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-830">Empty</span></span> | <span data-ttu-id="a4b89-831">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4b89-832">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4b89-833">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4b89-834">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4b89-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4b89-835">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4b89-836">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-837">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4b89-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4b89-838">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4b89-839">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto `Startup.ConfigureServices` dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a4b89-840">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4b89-841">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un oggetto `JsonSerializerSettings` JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4b89-842">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a4b89-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a4b89-843">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il `Startup.ConfigureServices`codice seguente in:</span><span class="sxs-lookup"><span data-stu-id="a4b89-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a4b89-844">Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4b89-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4b89-845">Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="a4b89-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4b89-846">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4b89-847">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-847">MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-848">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4b89-849">Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-850">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4b89-851">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="a4b89-851">Configure server options</span></span>

<span data-ttu-id="a4b89-852">La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4b89-853">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-853">Option</span></span> | <span data-ttu-id="a4b89-854">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-854">Default Value</span></span> | <span data-ttu-id="a4b89-855">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="a4b89-856">30 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-856">30 seconds</span></span> | <span data-ttu-id="a4b89-857">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="a4b89-858">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="a4b89-859">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="a4b89-860">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-860">15 seconds</span></span> | <span data-ttu-id="a4b89-861">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="a4b89-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4b89-862">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-863">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-864">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-864">15 seconds</span></span> | <span data-ttu-id="a4b89-865">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4b89-866">Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4b89-867">Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4b89-868">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="a4b89-868">All installed protocols</span></span> | <span data-ttu-id="a4b89-869">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-869">Protocols supported by this hub.</span></span> <span data-ttu-id="a4b89-870">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4b89-871">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4b89-872">Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a4b89-873">È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4b89-874">Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="a4b89-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4b89-875">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="a4b89-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4b89-876">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4b89-877">Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4b89-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4b89-878">La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4b89-879">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-879">Option</span></span> | <span data-ttu-id="a4b89-880">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-880">Default Value</span></span> | <span data-ttu-id="a4b89-881">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4b89-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-882">32 KB</span></span> | <span data-ttu-id="a4b89-883">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="a4b89-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a4b89-884">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4b89-885">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4b89-886">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4b89-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-887">32 KB</span></span> | <span data-ttu-id="a4b89-888">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="a4b89-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a4b89-889">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4b89-890">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-890">All Transports are enabled.</span></span> | <span data-ttu-id="a4b89-891">Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4b89-892">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-892">See below.</span></span> | <span data-ttu-id="a4b89-893">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4b89-894">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-894">See below.</span></span> | <span data-ttu-id="a4b89-895">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4b89-896">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4b89-897">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-897">Option</span></span> | <span data-ttu-id="a4b89-898">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-898">Default Value</span></span> | <span data-ttu-id="a4b89-899">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4b89-900">90 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-900">90 seconds</span></span> | <span data-ttu-id="a4b89-901">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4b89-902">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4b89-903">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4b89-904">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-904">Option</span></span> | <span data-ttu-id="a4b89-905">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-905">Default Value</span></span> | <span data-ttu-id="a4b89-906">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4b89-907">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-907">5 seconds</span></span> | <span data-ttu-id="a4b89-908">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="a4b89-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4b89-909">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4b89-910">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4b89-911">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="a4b89-911">Configure client options</span></span>

<span data-ttu-id="a4b89-912">Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4b89-913">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.</span><span class="sxs-lookup"><span data-stu-id="a4b89-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4b89-914">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="a4b89-914">Configure logging</span></span>

<span data-ttu-id="a4b89-915">La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4b89-916">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4b89-917">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-918">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="a4b89-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4b89-919">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="a4b89-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4b89-920">Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4b89-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4b89-921">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a4b89-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4b89-922">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4b89-923">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="a4b89-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4b89-924">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="a4b89-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4b89-925">Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4b89-926">Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4b89-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4b89-927">Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4b89-928">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a4b89-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4b89-929">Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4b89-930">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4b89-931">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4b89-932">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="a4b89-932">Configure allowed transports</span></span>

<span data-ttu-id="a4b89-933">I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4b89-934">Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4b89-935">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a4b89-935">All transports are enabled by default.</span></span>

<span data-ttu-id="a4b89-936">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="a4b89-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4b89-937">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="a4b89-938">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="a4b89-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4b89-939">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="a4b89-939">Configure bearer authentication</span></span>

<span data-ttu-id="a4b89-940">Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4b89-941">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="a4b89-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4b89-942">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4b89-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4b89-943">In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a4b89-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4b89-944">Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4b89-945">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4b89-946">Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4b89-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4b89-947">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4b89-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4b89-948">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4b89-949">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a4b89-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4b89-950">Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-951">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-952">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-952">Option</span></span> | <span data-ttu-id="a4b89-953">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-953">Default value</span></span> | <span data-ttu-id="a4b89-954">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4b89-955">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-956">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-956">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-957">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-958">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-959">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-960">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-960">15 seconds</span></span> | <span data-ttu-id="a4b89-961">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-962">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-963">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-964">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-965">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-965">15 seconds</span></span> | <span data-ttu-id="a4b89-966">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-967">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-968">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="a4b89-969">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="a4b89-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4b89-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-971">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-971">Option</span></span> | <span data-ttu-id="a4b89-972">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-972">Default value</span></span> | <span data-ttu-id="a4b89-973">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4b89-974">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-975">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-975">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-976">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4b89-977">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-978">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="a4b89-979">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-980">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-981">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-982">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-983">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-983">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-984">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-984">Option</span></span> | <span data-ttu-id="a4b89-985">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-985">Default value</span></span> | <span data-ttu-id="a4b89-986">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4b89-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4b89-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4b89-988">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-989">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-989">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-990">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-991">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-992">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4b89-993">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-993">15 seconds</span></span> | <span data-ttu-id="a4b89-994">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-995">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-996">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-997">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="a4b89-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="a4b89-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="a4b89-999">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-1000">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="a4b89-1001">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="a4b89-1002">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4b89-1003">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-1003">Configure additional options</span></span>

<span data-ttu-id="a4b89-1004">Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-1006">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-1006">.NET Option</span></span> |  <span data-ttu-id="a4b89-1007">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-1007">Default value</span></span> | <span data-ttu-id="a4b89-1008">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4b89-1009">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4b89-1010">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1011">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1012">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4b89-1013">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1013">Empty</span></span> | <span data-ttu-id="a4b89-1014">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4b89-1015">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1015">Empty</span></span> | <span data-ttu-id="a4b89-1016">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4b89-1017">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1017">Empty</span></span> | <span data-ttu-id="a4b89-1018">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4b89-1019">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1019">5 seconds</span></span> | <span data-ttu-id="a4b89-1020">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1020">WebSockets only.</span></span> <span data-ttu-id="a4b89-1021">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4b89-1022">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4b89-1023">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1023">Empty</span></span> | <span data-ttu-id="a4b89-1024">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4b89-1025">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4b89-1026">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4b89-1027">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4b89-1028">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4b89-1029">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="a4b89-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4b89-1030">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4b89-1031">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4b89-1032">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4b89-1033">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4b89-1034">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4b89-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-1036">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-1036">JavaScript Option</span></span> | <span data-ttu-id="a4b89-1037">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1037">Default Value</span></span> | <span data-ttu-id="a4b89-1038">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4b89-1039">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4b89-1040">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1041">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1042">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-1043">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-1044">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-1044">Java Option</span></span> | <span data-ttu-id="a4b89-1045">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1045">Default Value</span></span> | <span data-ttu-id="a4b89-1046">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4b89-1047">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4b89-1048">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1049">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1050">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4b89-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4b89-1052">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1052">Empty</span></span> | <span data-ttu-id="a4b89-1053">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4b89-1054">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4b89-1055">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4b89-1056">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4b89-1057">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="a4b89-1058">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-1059">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="a4b89-1060">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="a4b89-1061">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto `Startup.ConfigureServices` dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a4b89-1062">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="a4b89-1063">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un oggetto `JsonSerializerSettings` JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="a4b89-1064">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="a4b89-1065">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il `Startup.ConfigureServices`codice seguente in:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="a4b89-1066">Nel client .NET lo stesso `AddJsonProtocol` metodo di estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="a4b89-1067">Per `Microsoft.Extensions.DependencyInjection` risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="a4b89-1068">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="a4b89-1069">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="a4b89-1069">MessagePack serialization options</span></span>

<span data-ttu-id="a4b89-1070">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="a4b89-1071">Per ulteriori informazioni, vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-1072">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="a4b89-1073">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="a4b89-1073">Configure server options</span></span>

<span data-ttu-id="a4b89-1074">La tabella seguente descrive le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="a4b89-1075">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1075">Option</span></span> | <span data-ttu-id="a4b89-1076">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1076">Default Value</span></span> | <span data-ttu-id="a4b89-1077">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-1078">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1078">15 seconds</span></span> | <span data-ttu-id="a4b89-1079">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="a4b89-1080">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-1081">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="a4b89-1082">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1082">15 seconds</span></span> | <span data-ttu-id="a4b89-1083">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="a4b89-1084">Quando si `KeepAliveInterval`modifica, modificare `ServerTimeout` / `serverTimeoutInMilliseconds` l'impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="a4b89-1085">Il valore `ServerTimeout` / `serverTimeoutInMilliseconds` consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="a4b89-1086">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="a4b89-1086">All installed protocols</span></span> | <span data-ttu-id="a4b89-1087">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="a4b89-1088">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="a4b89-1089">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="a4b89-1090">Il valore predefinito `false`è, poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="a4b89-1091">È possibile configurare le opzioni per tutti gli hub fornendo le `AddSignalR` opzioni delegate alla chiamata in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="a4b89-1092">Le opzioni per un singolo Hub eseguono l'override delle opzioni `AddSignalR` globali fornite in e possono <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="a4b89-1093">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="a4b89-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="a4b89-1094">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="a4b89-1095">Queste opzioni vengono configurate passando un delegato [a\<MapHub T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="a4b89-1096">La tabella seguente descrive le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="a4b89-1097">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1097">Option</span></span> | <span data-ttu-id="a4b89-1098">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1098">Default Value</span></span> | <span data-ttu-id="a4b89-1099">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="a4b89-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-1100">32 KB</span></span> | <span data-ttu-id="a4b89-1101">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="a4b89-1102">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="a4b89-1103">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="a4b89-1104">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="a4b89-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="a4b89-1105">32 KB</span></span> | <span data-ttu-id="a4b89-1106">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="a4b89-1107">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="a4b89-1108">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1108">All Transports are enabled.</span></span> | <span data-ttu-id="a4b89-1109">Enumerazione dei flag di bit `HttpTransportType` di valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="a4b89-1110">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1110">See below.</span></span> | <span data-ttu-id="a4b89-1111">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="a4b89-1112">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1112">See below.</span></span> | <span data-ttu-id="a4b89-1113">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="a4b89-1114">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="a4b89-1115">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1115">Option</span></span> | <span data-ttu-id="a4b89-1116">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1116">Default Value</span></span> | <span data-ttu-id="a4b89-1117">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="a4b89-1118">90 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1118">90 seconds</span></span> | <span data-ttu-id="a4b89-1119">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="a4b89-1120">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="a4b89-1121">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="a4b89-1122">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1122">Option</span></span> | <span data-ttu-id="a4b89-1123">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1123">Default Value</span></span> | <span data-ttu-id="a4b89-1124">Description</span><span class="sxs-lookup"><span data-stu-id="a4b89-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="a4b89-1125">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1125">5 seconds</span></span> | <span data-ttu-id="a4b89-1126">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="a4b89-1127">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="a4b89-1128">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="a4b89-1129">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="a4b89-1129">Configure client options</span></span>

<span data-ttu-id="a4b89-1130">Le `HubConnectionBuilder` opzioni client possono essere configurate nel tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="a4b89-1131">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a `HubConnection` quella di.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="a4b89-1132">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1132">Configure logging</span></span>

<span data-ttu-id="a4b89-1133">La registrazione viene configurata nel client .NET `ConfigureLogging` utilizzando il metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="a4b89-1134">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="a4b89-1135">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="a4b89-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="a4b89-1136">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="a4b89-1137">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="a4b89-1138">Ad esempio, per abilitare la registrazione della console, `Microsoft.Extensions.Logging.Console` installare il pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="a4b89-1139">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="a4b89-1140">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="a4b89-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="a4b89-1141">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="a4b89-1142">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="a4b89-1143">Per disabilitare completamente la `signalR.LogLevel.None` `configureLogging` registrazione, specificare nel metodo.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="a4b89-1144">Per ulteriori informazioni sulla registrazione, vedere la [documentazione di diagnostica di SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="a4b89-1145">Il client Java SignalR usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="a4b89-1146">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="a4b89-1147">Il frammento di codice seguente illustra come `java.util.logging` usare con il client Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="a4b89-1148">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="a4b89-1149">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="a4b89-1150">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="a4b89-1150">Configure allowed transports</span></span>

<span data-ttu-id="a4b89-1151">I trasporti usati da SignalR possono essere configurati nella `WithUrl` chiamata (`withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="a4b89-1152">Un operatore OR bit per bit dei valori `HttpTransportType` di può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="a4b89-1153">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="a4b89-1154">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="a4b89-1155">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="a4b89-1156">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="a4b89-1156">Configure bearer authentication</span></span>

<span data-ttu-id="a4b89-1157">Per fornire i dati di autenticazione insieme alle richieste SignalR, `AccessTokenProvider` usare l'`accessTokenFactory` opzione (in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="a4b89-1158">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="a4b89-1159">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="a4b89-1160">In questi casi, il token di accesso viene fornito come valore `access_token`stringa di query.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="a4b89-1161">Nel client .NET è possibile specificare `AccessTokenProvider` l'opzione usando il delegato options in `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="a4b89-1162">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="a4b89-1163">Nel client Java SignalR è possibile configurare un bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="a4b89-1164">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [RxJava](https://github.com/ReactiveX/RxJava) [stringa a\<>singola ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="a4b89-1165">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="a4b89-1166">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="a4b89-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="a4b89-1167">Opzioni aggiuntive per la configurazione del timeout e del `HubConnection` comportamento keep-alive sono disponibili nell'oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-1169">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1169">Option</span></span> | <span data-ttu-id="a4b89-1170">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-1170">Default value</span></span> | <span data-ttu-id="a4b89-1171">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="a4b89-1172">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-1173">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1173">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-1174">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `Closed` attiva l'`onclose` evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-1175">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-1176">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="a4b89-1177">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1177">15 seconds</span></span> | <span data-ttu-id="a4b89-1178">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-1179">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento (`onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="a4b89-1180">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-1181">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="a4b89-1182">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="a4b89-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-1184">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1184">Option</span></span> | <span data-ttu-id="a4b89-1185">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-1185">Default value</span></span> | <span data-ttu-id="a4b89-1186">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="a4b89-1187">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-1188">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1188">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-1189">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onclose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="a4b89-1190">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-1191">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-1192">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-1193">Opzione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1193">Option</span></span> | <span data-ttu-id="a4b89-1194">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-1194">Default value</span></span> | <span data-ttu-id="a4b89-1195">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="a4b89-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="a4b89-1197">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="a4b89-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="a4b89-1198">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1198">Timeout for server activity.</span></span> <span data-ttu-id="a4b89-1199">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e `onClose` attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-1200">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="a4b89-1201">Il valore consigliato è un numero almeno il doppio del `KeepAliveInterval` valore del server, per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="a4b89-1202">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1202">15 seconds</span></span> | <span data-ttu-id="a4b89-1203">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="a4b89-1204">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="a4b89-1205">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="a4b89-1206">Per informazioni dettagliate sul processo di handshake, vedere la [specifica del protocollo dell'hub SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="a4b89-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="a4b89-1207">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-1207">Configure additional options</span></span>

<span data-ttu-id="a4b89-1208">Opzioni aggiuntive possono essere configurate `WithUrl` nel`withUrl` metodo (in JavaScript) `HubConnectionBuilder` in o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="a4b89-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="a4b89-1210">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="a4b89-1210">.NET Option</span></span> |  <span data-ttu-id="a4b89-1211">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="a4b89-1211">Default value</span></span> | <span data-ttu-id="a4b89-1212">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="a4b89-1213">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="a4b89-1214">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1215">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1216">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="a4b89-1217">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1217">Empty</span></span> | <span data-ttu-id="a4b89-1218">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="a4b89-1219">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1219">Empty</span></span> | <span data-ttu-id="a4b89-1220">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="a4b89-1221">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1221">Empty</span></span> | <span data-ttu-id="a4b89-1222">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="a4b89-1223">5 secondi</span><span class="sxs-lookup"><span data-stu-id="a4b89-1223">5 seconds</span></span> | <span data-ttu-id="a4b89-1224">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1224">WebSockets only.</span></span> <span data-ttu-id="a4b89-1225">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="a4b89-1226">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="a4b89-1227">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1227">Empty</span></span> | <span data-ttu-id="a4b89-1228">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="a4b89-1229">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="a4b89-1230">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="a4b89-1231">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="a4b89-1232">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="a4b89-1233">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si desidera gestire dal gestore fornito. in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="a4b89-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="a4b89-1234">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="a4b89-1235">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="a4b89-1236">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="a4b89-1237">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="a4b89-1238">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="a4b89-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="a4b89-1240">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="a4b89-1240">JavaScript Option</span></span> | <span data-ttu-id="a4b89-1241">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1241">Default Value</span></span> | <span data-ttu-id="a4b89-1242">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="a4b89-1243">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="a4b89-1244">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1245">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1246">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="a4b89-1247">Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="a4b89-1248">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="a4b89-1248">Java Option</span></span> | <span data-ttu-id="a4b89-1249">Default Value</span><span class="sxs-lookup"><span data-stu-id="a4b89-1249">Default Value</span></span> | <span data-ttu-id="a4b89-1250">Descrizione</span><span class="sxs-lookup"><span data-stu-id="a4b89-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="a4b89-1251">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="a4b89-1252">Impostare questa impostazione `true` su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="a4b89-1253">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="a4b89-1254">Questa impostazione non può essere abilitata quando si usa il servizio Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="a4b89-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="a4b89-1256">Vuoto</span><span class="sxs-lookup"><span data-stu-id="a4b89-1256">Empty</span></span> | <span data-ttu-id="a4b89-1257">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="a4b89-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="a4b89-1258">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="a4b89-1259">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="a4b89-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="a4b89-1260">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="a4b89-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="a4b89-1261">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a4b89-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
