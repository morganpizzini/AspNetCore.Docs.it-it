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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="347a9-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="347a9-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="347a9-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="347a9-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="347a9-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="347a9-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="347a9-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol.JSON](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) serialization can be configured on the server using the AddJsonProtocol extension method.</span><span class="sxs-lookup"><span data-stu-id="347a9-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="347a9-108">`AddJsonProtocol`può essere aggiunto dopo `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="347a9-109">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="347a9-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="347a9-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) di `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> tale oggetto è un oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="347a9-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="347a9-111">Per ulteriori informazioni, vedere la [documentazione di System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="347a9-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="347a9-112">Ad esempio, per configurare il serializzatore in modo da non modificare le maiuscole e le `Startup.ConfigureServices`minuscole dei nomi delle proprietà, anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in :</span><span class="sxs-lookup"><span data-stu-id="347a9-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="347a9-113">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="347a9-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="347a9-114">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="347a9-115">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="347a9-116">Passare a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="347a9-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="347a9-117">Se sono necessarie `Newtonsoft.Json` funzionalità non supportate in `System.Text.Json`, vedere Passare a [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="347a9-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="347a9-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-118">MessagePack serialization options</span></span>

<span data-ttu-id="347a9-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="347a9-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="347a9-120">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="347a9-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-121">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="347a9-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="347a9-122">Configure server options</span></span>

<span data-ttu-id="347a9-123">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="347a9-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-124">Option</span></span> | <span data-ttu-id="347a9-125">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-125">Default Value</span></span> | <span data-ttu-id="347a9-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="347a9-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-127">30 seconds</span></span> | <span data-ttu-id="347a9-128">Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="347a9-129">Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="347a9-130">Il valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="347a9-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-131">15 seconds</span></span> | <span data-ttu-id="347a9-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="347a9-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="347a9-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-134">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-135">15 seconds</span></span> | <span data-ttu-id="347a9-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="347a9-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="347a9-137">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="347a9-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="347a9-138">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="347a9-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="347a9-139">All installed protocols</span></span> | <span data-ttu-id="347a9-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-140">Protocols supported by this hub.</span></span> <span data-ttu-id="347a9-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="347a9-142">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="347a9-143">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="347a9-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="347a9-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="347a9-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="347a9-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi di flusso.</span><span class="sxs-lookup"><span data-stu-id="347a9-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="347a9-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-146">32 KB</span></span> | <span data-ttu-id="347a9-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="347a9-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="347a9-148">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="347a9-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="347a9-149">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="347a9-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="347a9-150">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="347a9-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="347a9-151">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="347a9-152">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="347a9-153">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="347a9-154">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-154">Option</span></span> | <span data-ttu-id="347a9-155">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-155">Default Value</span></span> | <span data-ttu-id="347a9-156">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="347a9-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-157">32 KB</span></span> | <span data-ttu-id="347a9-158">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la pressione arretrata.</span><span class="sxs-lookup"><span data-stu-id="347a9-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="347a9-159">L'aumento di questo valore consente al server di ricevere messaggi più grandi più rapidamente senza applicare la pressione a rito, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="347a9-160">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="347a9-161">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="347a9-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-162">32 KB</span></span> | <span data-ttu-id="347a9-163">Numero massimo di byte inviati dall'app memorizzati nel buffer del server prima di osservare la retropressione.</span><span class="sxs-lookup"><span data-stu-id="347a9-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="347a9-164">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi più grandi più rapidamente senza attendere la pressione a riutilizzo, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="347a9-165">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="347a9-165">All Transports are enabled.</span></span> | <span data-ttu-id="347a9-166">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="347a9-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="347a9-167">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-167">See below.</span></span> | <span data-ttu-id="347a9-168">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="347a9-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="347a9-169">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-169">See below.</span></span> | <span data-ttu-id="347a9-170">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="347a9-171">0</span><span class="sxs-lookup"><span data-stu-id="347a9-171">0</span></span> | <span data-ttu-id="347a9-172">Specificare la versione minima del protocollo di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="347a9-173">Viene utilizzato per limitare i client alle versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="347a9-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="347a9-174">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="347a9-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="347a9-175">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-175">Option</span></span> | <span data-ttu-id="347a9-176">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-176">Default Value</span></span> | <span data-ttu-id="347a9-177">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="347a9-178">90 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-178">90 seconds</span></span> | <span data-ttu-id="347a9-179">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="347a9-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="347a9-180">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="347a9-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="347a9-181">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="347a9-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="347a9-182">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-182">Option</span></span> | <span data-ttu-id="347a9-183">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-183">Default Value</span></span> | <span data-ttu-id="347a9-184">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="347a9-185">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-185">5 seconds</span></span> | <span data-ttu-id="347a9-186">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="347a9-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="347a9-187">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="347a9-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="347a9-188">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="347a9-189">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="347a9-189">Configure client options</span></span>

<span data-ttu-id="347a9-190">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="347a9-191">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="347a9-192">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="347a9-192">Configure logging</span></span>

<span data-ttu-id="347a9-193">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="347a9-194">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="347a9-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="347a9-195">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="347a9-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-196">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="347a9-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="347a9-197">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="347a9-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="347a9-198">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="347a9-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="347a9-199">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="347a9-200">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="347a9-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="347a9-201">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="347a9-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="347a9-202">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="347a9-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="347a9-203">Anziché `LogLevel` un valore, è `string` anche possibile fornire un valore che rappresenta un nome di livello di log.</span><span class="sxs-lookup"><span data-stu-id="347a9-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="347a9-204">Ciò è utile quando si configura la registrazione SignalR `LogLevel` in ambienti in cui non si ha accesso alle costanti.</span><span class="sxs-lookup"><span data-stu-id="347a9-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="347a9-205">Nella tabella seguente sono elencati i livelli di registrazione disponibili.</span><span class="sxs-lookup"><span data-stu-id="347a9-205">The following table lists the available log levels.</span></span> <span data-ttu-id="347a9-206">Il valore da `configureLogging` specificare per imposta il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="347a9-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="347a9-207">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo di esso nella tabella.**</span><span class="sxs-lookup"><span data-stu-id="347a9-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="347a9-208">string</span><span class="sxs-lookup"><span data-stu-id="347a9-208">String</span></span>                      | <span data-ttu-id="347a9-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="347a9-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="347a9-210">`info`**o o**`information`</span><span class="sxs-lookup"><span data-stu-id="347a9-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="347a9-211">`warn`**o o**`warning`</span><span class="sxs-lookup"><span data-stu-id="347a9-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="347a9-212">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="347a9-213">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="347a9-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="347a9-214">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="347a9-215">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="347a9-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="347a9-216">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="347a9-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="347a9-217">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="347a9-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="347a9-218">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="347a9-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="347a9-219">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="347a9-219">Configure allowed transports</span></span>

<span data-ttu-id="347a9-220">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="347a9-221">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="347a9-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="347a9-222">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="347a9-222">All transports are enabled by default.</span></span>

<span data-ttu-id="347a9-223">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="347a9-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="347a9-224">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="347a9-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="347a9-225">In questa versione di Websockets client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="347a9-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="347a9-226">Nel client Java il trasporto viene `withTransport` selezionato `HttpHubConnectionBuilder`con il metodo sul metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="347a9-227">Per impostazione predefinita, il client Java utilizza il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="347a9-228">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="347a9-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="347a9-229">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="347a9-229">Configure bearer authentication</span></span>

<span data-ttu-id="347a9-230">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="347a9-231">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="347a9-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="347a9-232">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="347a9-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="347a9-233">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="347a9-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="347a9-234">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="347a9-235">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="347a9-236">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="347a9-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="347a9-237">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="347a9-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="347a9-238">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="347a9-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="347a9-239">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="347a9-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="347a9-240">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="347a9-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-241">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-242">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-242">Option</span></span> | <span data-ttu-id="347a9-243">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-243">Default value</span></span> | <span data-ttu-id="347a9-244">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="347a9-245">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-246">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-246">Timeout for server activity.</span></span> <span data-ttu-id="347a9-247">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-248">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-249">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="347a9-250">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-250">15 seconds</span></span> | <span data-ttu-id="347a9-251">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-252">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-253">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-254">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-255">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-255">15 seconds</span></span> | <span data-ttu-id="347a9-256">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-257">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-258">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="347a9-259">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="347a9-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="347a9-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-261">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-261">Option</span></span> | <span data-ttu-id="347a9-262">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-262">Default value</span></span> | <span data-ttu-id="347a9-263">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="347a9-264">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-265">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-265">Timeout for server activity.</span></span> <span data-ttu-id="347a9-266">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="347a9-267">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-268">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="347a9-269">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-270">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-271">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-272">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-273">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-273">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-274">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-274">Option</span></span> | <span data-ttu-id="347a9-275">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-275">Default value</span></span> | <span data-ttu-id="347a9-276">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="347a9-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="347a9-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="347a9-278">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-279">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-279">Timeout for server activity.</span></span> <span data-ttu-id="347a9-280">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-281">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-282">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="347a9-283">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-283">15 seconds</span></span> | <span data-ttu-id="347a9-284">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-285">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-286">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-287">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="347a9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="347a9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="347a9-289">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-290">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-291">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-292">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="347a9-293">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-293">Configure additional options</span></span>

<span data-ttu-id="347a9-294">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="347a9-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-295">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-296">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="347a9-296">.NET Option</span></span> |  <span data-ttu-id="347a9-297">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-297">Default value</span></span> | <span data-ttu-id="347a9-298">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="347a9-299">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="347a9-300">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-301">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-302">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="347a9-303">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-303">Empty</span></span> | <span data-ttu-id="347a9-304">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="347a9-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="347a9-305">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-305">Empty</span></span> | <span data-ttu-id="347a9-306">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="347a9-307">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-307">Empty</span></span> | <span data-ttu-id="347a9-308">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="347a9-309">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-309">5 seconds</span></span> | <span data-ttu-id="347a9-310">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-310">WebSockets only.</span></span> <span data-ttu-id="347a9-311">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="347a9-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="347a9-312">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="347a9-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="347a9-313">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-313">Empty</span></span> | <span data-ttu-id="347a9-314">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="347a9-315">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="347a9-316">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="347a9-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="347a9-317">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="347a9-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="347a9-318">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="347a9-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="347a9-319">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="347a9-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="347a9-320">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="347a9-321">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="347a9-322">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="347a9-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="347a9-323">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="347a9-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="347a9-324">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="347a9-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="347a9-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-326">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-326">JavaScript Option</span></span> | <span data-ttu-id="347a9-327">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-327">Default Value</span></span> | <span data-ttu-id="347a9-328">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="347a9-329">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="347a9-330">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-331">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-332">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-333">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-333">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-334">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="347a9-334">Java Option</span></span> | <span data-ttu-id="347a9-335">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-335">Default Value</span></span> | <span data-ttu-id="347a9-336">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="347a9-337">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="347a9-338">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-339">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-340">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="347a9-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="347a9-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="347a9-342">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-342">Empty</span></span> | <span data-ttu-id="347a9-343">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="347a9-344">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="347a9-345">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="347a9-346">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="347a9-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="347a9-347">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="347a9-348">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="347a9-349">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="347a9-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="347a9-350">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="347a9-351">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol.JSON](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) serialization can be configured on the server using the AddJsonProtocol extension method.</span><span class="sxs-lookup"><span data-stu-id="347a9-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="347a9-352">`AddJsonProtocol`può essere aggiunto dopo `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="347a9-353">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="347a9-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="347a9-354">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) di `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> tale oggetto è un oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="347a9-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="347a9-355">Per ulteriori informazioni, vedere la [documentazione di System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="347a9-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="347a9-356">Ad esempio, per configurare il serializzatore in modo da non modificare le maiuscole e le `Startup.ConfigureServices`minuscole dei nomi delle proprietà, anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in :</span><span class="sxs-lookup"><span data-stu-id="347a9-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="347a9-357">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="347a9-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="347a9-358">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="347a9-359">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="347a9-360">Passare a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="347a9-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="347a9-361">Se sono necessarie `Newtonsoft.Json` funzionalità non supportate in `System.Text.Json`, vedere Passare a [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="347a9-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="347a9-362">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-362">MessagePack serialization options</span></span>

<span data-ttu-id="347a9-363">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="347a9-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="347a9-364">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="347a9-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-365">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="347a9-366">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="347a9-366">Configure server options</span></span>

<span data-ttu-id="347a9-367">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="347a9-368">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-368">Option</span></span> | <span data-ttu-id="347a9-369">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-369">Default Value</span></span> | <span data-ttu-id="347a9-370">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="347a9-371">30 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-371">30 seconds</span></span> | <span data-ttu-id="347a9-372">Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="347a9-373">Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="347a9-374">Il valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="347a9-375">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-375">15 seconds</span></span> | <span data-ttu-id="347a9-376">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="347a9-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="347a9-377">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-378">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-379">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-379">15 seconds</span></span> | <span data-ttu-id="347a9-380">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="347a9-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="347a9-381">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="347a9-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="347a9-382">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="347a9-383">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="347a9-383">All installed protocols</span></span> | <span data-ttu-id="347a9-384">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-384">Protocols supported by this hub.</span></span> <span data-ttu-id="347a9-385">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="347a9-386">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="347a9-387">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="347a9-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="347a9-388">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="347a9-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="347a9-389">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi di flusso.</span><span class="sxs-lookup"><span data-stu-id="347a9-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="347a9-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-390">32 KB</span></span> | <span data-ttu-id="347a9-391">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="347a9-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="347a9-392">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="347a9-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="347a9-393">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="347a9-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="347a9-394">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="347a9-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="347a9-395">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="347a9-396">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="347a9-397">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="347a9-398">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-398">Option</span></span> | <span data-ttu-id="347a9-399">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-399">Default Value</span></span> | <span data-ttu-id="347a9-400">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="347a9-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-401">32 KB</span></span> | <span data-ttu-id="347a9-402">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la pressione arretrata.</span><span class="sxs-lookup"><span data-stu-id="347a9-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="347a9-403">L'aumento di questo valore consente al server di ricevere messaggi più grandi più rapidamente senza applicare la pressione a rito, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="347a9-404">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="347a9-405">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="347a9-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-406">32 KB</span></span> | <span data-ttu-id="347a9-407">Numero massimo di byte inviati dall'app memorizzati nel buffer del server prima di osservare la retropressione.</span><span class="sxs-lookup"><span data-stu-id="347a9-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="347a9-408">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi più grandi più rapidamente senza attendere la pressione a riutilizzo, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="347a9-409">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="347a9-409">All Transports are enabled.</span></span> | <span data-ttu-id="347a9-410">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="347a9-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="347a9-411">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-411">See below.</span></span> | <span data-ttu-id="347a9-412">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="347a9-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="347a9-413">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-413">See below.</span></span> | <span data-ttu-id="347a9-414">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="347a9-415">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="347a9-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="347a9-416">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-416">Option</span></span> | <span data-ttu-id="347a9-417">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-417">Default Value</span></span> | <span data-ttu-id="347a9-418">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="347a9-419">90 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-419">90 seconds</span></span> | <span data-ttu-id="347a9-420">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="347a9-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="347a9-421">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="347a9-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="347a9-422">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="347a9-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="347a9-423">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-423">Option</span></span> | <span data-ttu-id="347a9-424">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-424">Default Value</span></span> | <span data-ttu-id="347a9-425">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="347a9-426">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-426">5 seconds</span></span> | <span data-ttu-id="347a9-427">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="347a9-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="347a9-428">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="347a9-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="347a9-429">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="347a9-430">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="347a9-430">Configure client options</span></span>

<span data-ttu-id="347a9-431">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="347a9-432">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="347a9-433">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="347a9-433">Configure logging</span></span>

<span data-ttu-id="347a9-434">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="347a9-435">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="347a9-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="347a9-436">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="347a9-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-437">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="347a9-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="347a9-438">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="347a9-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="347a9-439">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="347a9-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="347a9-440">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="347a9-441">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="347a9-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="347a9-442">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="347a9-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="347a9-443">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="347a9-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="347a9-444">Anziché `LogLevel` un valore, è `string` anche possibile fornire un valore che rappresenta un nome di livello di log.</span><span class="sxs-lookup"><span data-stu-id="347a9-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="347a9-445">Ciò è utile quando si configura la registrazione SignalR `LogLevel` in ambienti in cui non si ha accesso alle costanti.</span><span class="sxs-lookup"><span data-stu-id="347a9-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="347a9-446">Nella tabella seguente sono elencati i livelli di registrazione disponibili.</span><span class="sxs-lookup"><span data-stu-id="347a9-446">The following table lists the available log levels.</span></span> <span data-ttu-id="347a9-447">Il valore da `configureLogging` specificare per imposta il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="347a9-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="347a9-448">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo di esso nella tabella.**</span><span class="sxs-lookup"><span data-stu-id="347a9-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="347a9-449">string</span><span class="sxs-lookup"><span data-stu-id="347a9-449">String</span></span>                      | <span data-ttu-id="347a9-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="347a9-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="347a9-451">`info`**o o**`information`</span><span class="sxs-lookup"><span data-stu-id="347a9-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="347a9-452">`warn`**o o**`warning`</span><span class="sxs-lookup"><span data-stu-id="347a9-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="347a9-453">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="347a9-454">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="347a9-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="347a9-455">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="347a9-456">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="347a9-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="347a9-457">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="347a9-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="347a9-458">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="347a9-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="347a9-459">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="347a9-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="347a9-460">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="347a9-460">Configure allowed transports</span></span>

<span data-ttu-id="347a9-461">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="347a9-462">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="347a9-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="347a9-463">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="347a9-463">All transports are enabled by default.</span></span>

<span data-ttu-id="347a9-464">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="347a9-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="347a9-465">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="347a9-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="347a9-466">In questa versione di Websockets client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="347a9-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="347a9-467">Nel client Java il trasporto viene `withTransport` selezionato `HttpHubConnectionBuilder`con il metodo sul metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="347a9-468">Per impostazione predefinita, il client Java utilizza il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="347a9-469">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="347a9-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="347a9-470">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="347a9-470">Configure bearer authentication</span></span>

<span data-ttu-id="347a9-471">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="347a9-472">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="347a9-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="347a9-473">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="347a9-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="347a9-474">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="347a9-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="347a9-475">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="347a9-476">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="347a9-477">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="347a9-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="347a9-478">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="347a9-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="347a9-479">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="347a9-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="347a9-480">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="347a9-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="347a9-481">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="347a9-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-482">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-483">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-483">Option</span></span> | <span data-ttu-id="347a9-484">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-484">Default value</span></span> | <span data-ttu-id="347a9-485">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="347a9-486">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-487">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-487">Timeout for server activity.</span></span> <span data-ttu-id="347a9-488">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-489">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-490">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="347a9-491">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-491">15 seconds</span></span> | <span data-ttu-id="347a9-492">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-493">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-494">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-495">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-496">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-496">15 seconds</span></span> | <span data-ttu-id="347a9-497">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-498">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-499">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="347a9-500">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="347a9-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="347a9-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-502">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-502">Option</span></span> | <span data-ttu-id="347a9-503">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-503">Default value</span></span> | <span data-ttu-id="347a9-504">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="347a9-505">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-506">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-506">Timeout for server activity.</span></span> <span data-ttu-id="347a9-507">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="347a9-508">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-509">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="347a9-510">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-511">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-512">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-513">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-514">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-514">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-515">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-515">Option</span></span> | <span data-ttu-id="347a9-516">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-516">Default value</span></span> | <span data-ttu-id="347a9-517">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="347a9-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="347a9-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="347a9-519">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-520">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-520">Timeout for server activity.</span></span> <span data-ttu-id="347a9-521">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-522">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-523">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="347a9-524">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-524">15 seconds</span></span> | <span data-ttu-id="347a9-525">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-526">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-527">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-528">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="347a9-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="347a9-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="347a9-530">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-531">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-532">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-533">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="347a9-534">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-534">Configure additional options</span></span>

<span data-ttu-id="347a9-535">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="347a9-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-536">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-537">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="347a9-537">.NET Option</span></span> |  <span data-ttu-id="347a9-538">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-538">Default value</span></span> | <span data-ttu-id="347a9-539">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="347a9-540">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="347a9-541">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-542">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-543">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="347a9-544">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-544">Empty</span></span> | <span data-ttu-id="347a9-545">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="347a9-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="347a9-546">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-546">Empty</span></span> | <span data-ttu-id="347a9-547">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="347a9-548">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-548">Empty</span></span> | <span data-ttu-id="347a9-549">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="347a9-550">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-550">5 seconds</span></span> | <span data-ttu-id="347a9-551">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-551">WebSockets only.</span></span> <span data-ttu-id="347a9-552">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="347a9-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="347a9-553">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="347a9-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="347a9-554">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-554">Empty</span></span> | <span data-ttu-id="347a9-555">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="347a9-556">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="347a9-557">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="347a9-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="347a9-558">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="347a9-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="347a9-559">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="347a9-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="347a9-560">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="347a9-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="347a9-561">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="347a9-562">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="347a9-563">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="347a9-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="347a9-564">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="347a9-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="347a9-565">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="347a9-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="347a9-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-567">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-567">JavaScript Option</span></span> | <span data-ttu-id="347a9-568">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-568">Default Value</span></span> | <span data-ttu-id="347a9-569">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="347a9-570">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="347a9-571">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-572">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-573">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-574">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-574">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-575">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="347a9-575">Java Option</span></span> | <span data-ttu-id="347a9-576">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-576">Default Value</span></span> | <span data-ttu-id="347a9-577">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="347a9-578">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="347a9-579">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-580">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-581">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="347a9-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="347a9-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="347a9-583">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-583">Empty</span></span> | <span data-ttu-id="347a9-584">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="347a9-585">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="347a9-586">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="347a9-587">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="347a9-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="347a9-588">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="347a9-589">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="347a9-590">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="347a9-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="347a9-591">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="347a9-592">La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="347a9-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="347a9-593">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="347a9-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="347a9-594">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="347a9-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="347a9-595">Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="347a9-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="347a9-596">Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="347a9-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="347a9-597">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="347a9-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="347a9-598">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="347a9-599">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="347a9-600">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-600">MessagePack serialization options</span></span>

<span data-ttu-id="347a9-601">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="347a9-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="347a9-602">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="347a9-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-603">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="347a9-604">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="347a9-604">Configure server options</span></span>

<span data-ttu-id="347a9-605">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="347a9-606">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-606">Option</span></span> | <span data-ttu-id="347a9-607">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-607">Default Value</span></span> | <span data-ttu-id="347a9-608">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="347a9-609">30 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-609">30 seconds</span></span> | <span data-ttu-id="347a9-610">Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="347a9-611">Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="347a9-612">Il valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="347a9-613">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-613">15 seconds</span></span> | <span data-ttu-id="347a9-614">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="347a9-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="347a9-615">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-616">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-617">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-617">15 seconds</span></span> | <span data-ttu-id="347a9-618">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="347a9-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="347a9-619">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="347a9-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="347a9-620">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="347a9-621">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="347a9-621">All installed protocols</span></span> | <span data-ttu-id="347a9-622">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-622">Protocols supported by this hub.</span></span> <span data-ttu-id="347a9-623">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="347a9-624">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="347a9-625">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="347a9-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="347a9-626">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="347a9-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="347a9-627">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="347a9-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="347a9-628">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="347a9-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="347a9-629">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="347a9-630">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="347a9-631">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="347a9-632">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-632">Option</span></span> | <span data-ttu-id="347a9-633">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-633">Default Value</span></span> | <span data-ttu-id="347a9-634">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="347a9-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-635">32 KB</span></span> | <span data-ttu-id="347a9-636">Numero massimo di byte ricevuti dal client memorizzato nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="347a9-637">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="347a9-638">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="347a9-639">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="347a9-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-640">32 KB</span></span> | <span data-ttu-id="347a9-641">Numero massimo di byte inviati dall'app memorizzati nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="347a9-642">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="347a9-643">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="347a9-643">All Transports are enabled.</span></span> | <span data-ttu-id="347a9-644">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="347a9-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="347a9-645">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-645">See below.</span></span> | <span data-ttu-id="347a9-646">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="347a9-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="347a9-647">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-647">See below.</span></span> | <span data-ttu-id="347a9-648">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="347a9-649">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="347a9-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="347a9-650">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-650">Option</span></span> | <span data-ttu-id="347a9-651">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-651">Default Value</span></span> | <span data-ttu-id="347a9-652">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="347a9-653">90 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-653">90 seconds</span></span> | <span data-ttu-id="347a9-654">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="347a9-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="347a9-655">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="347a9-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="347a9-656">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="347a9-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="347a9-657">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-657">Option</span></span> | <span data-ttu-id="347a9-658">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-658">Default Value</span></span> | <span data-ttu-id="347a9-659">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="347a9-660">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-660">5 seconds</span></span> | <span data-ttu-id="347a9-661">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="347a9-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="347a9-662">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="347a9-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="347a9-663">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="347a9-664">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="347a9-664">Configure client options</span></span>

<span data-ttu-id="347a9-665">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="347a9-666">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="347a9-667">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="347a9-667">Configure logging</span></span>

<span data-ttu-id="347a9-668">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="347a9-669">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="347a9-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="347a9-670">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="347a9-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-671">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="347a9-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="347a9-672">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="347a9-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="347a9-673">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="347a9-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="347a9-674">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="347a9-675">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="347a9-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="347a9-676">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="347a9-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="347a9-677">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="347a9-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="347a9-678">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="347a9-679">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="347a9-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="347a9-680">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="347a9-681">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="347a9-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="347a9-682">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="347a9-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="347a9-683">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="347a9-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="347a9-684">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="347a9-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="347a9-685">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="347a9-685">Configure allowed transports</span></span>

<span data-ttu-id="347a9-686">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="347a9-687">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="347a9-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="347a9-688">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="347a9-688">All transports are enabled by default.</span></span>

<span data-ttu-id="347a9-689">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="347a9-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="347a9-690">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="347a9-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="347a9-691">In questa versione di Websockets client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="347a9-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="347a9-692">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="347a9-692">Configure bearer authentication</span></span>

<span data-ttu-id="347a9-693">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="347a9-694">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="347a9-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="347a9-695">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="347a9-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="347a9-696">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="347a9-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="347a9-697">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="347a9-698">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="347a9-699">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="347a9-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="347a9-700">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="347a9-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="347a9-701">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="347a9-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="347a9-702">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="347a9-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="347a9-703">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="347a9-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-704">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-705">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-705">Option</span></span> | <span data-ttu-id="347a9-706">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-706">Default value</span></span> | <span data-ttu-id="347a9-707">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="347a9-708">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-709">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-709">Timeout for server activity.</span></span> <span data-ttu-id="347a9-710">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-711">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-712">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="347a9-713">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-713">15 seconds</span></span> | <span data-ttu-id="347a9-714">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-715">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-716">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-717">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-718">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-718">15 seconds</span></span> | <span data-ttu-id="347a9-719">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-720">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-721">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="347a9-722">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="347a9-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="347a9-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-724">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-724">Option</span></span> | <span data-ttu-id="347a9-725">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-725">Default value</span></span> | <span data-ttu-id="347a9-726">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="347a9-727">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-728">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-728">Timeout for server activity.</span></span> <span data-ttu-id="347a9-729">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="347a9-730">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-731">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="347a9-732">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-733">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-734">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-735">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-736">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-736">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-737">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-737">Option</span></span> | <span data-ttu-id="347a9-738">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-738">Default value</span></span> | <span data-ttu-id="347a9-739">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="347a9-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="347a9-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="347a9-741">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-742">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-742">Timeout for server activity.</span></span> <span data-ttu-id="347a9-743">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-744">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-745">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="347a9-746">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-746">15 seconds</span></span> | <span data-ttu-id="347a9-747">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-748">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-749">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-750">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="347a9-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="347a9-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="347a9-752">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="347a9-753">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="347a9-754">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="347a9-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="347a9-755">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="347a9-756">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-756">Configure additional options</span></span>

<span data-ttu-id="347a9-757">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="347a9-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-758">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-759">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="347a9-759">.NET Option</span></span> |  <span data-ttu-id="347a9-760">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-760">Default value</span></span> | <span data-ttu-id="347a9-761">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="347a9-762">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="347a9-763">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-764">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-765">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="347a9-766">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-766">Empty</span></span> | <span data-ttu-id="347a9-767">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="347a9-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="347a9-768">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-768">Empty</span></span> | <span data-ttu-id="347a9-769">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="347a9-770">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-770">Empty</span></span> | <span data-ttu-id="347a9-771">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="347a9-772">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-772">5 seconds</span></span> | <span data-ttu-id="347a9-773">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-773">WebSockets only.</span></span> <span data-ttu-id="347a9-774">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="347a9-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="347a9-775">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="347a9-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="347a9-776">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-776">Empty</span></span> | <span data-ttu-id="347a9-777">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="347a9-778">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="347a9-779">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="347a9-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="347a9-780">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="347a9-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="347a9-781">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="347a9-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="347a9-782">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="347a9-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="347a9-783">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="347a9-784">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="347a9-785">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="347a9-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="347a9-786">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="347a9-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="347a9-787">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="347a9-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="347a9-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-789">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-789">JavaScript Option</span></span> | <span data-ttu-id="347a9-790">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-790">Default Value</span></span> | <span data-ttu-id="347a9-791">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="347a9-792">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="347a9-793">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-794">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-795">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-796">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-796">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-797">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="347a9-797">Java Option</span></span> | <span data-ttu-id="347a9-798">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-798">Default Value</span></span> | <span data-ttu-id="347a9-799">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="347a9-800">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="347a9-801">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-802">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-803">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="347a9-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="347a9-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="347a9-805">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-805">Empty</span></span> | <span data-ttu-id="347a9-806">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="347a9-807">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="347a9-808">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="347a9-809">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="347a9-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="347a9-810">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="347a9-811">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="347a9-812">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="347a9-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="347a9-813">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="347a9-814">La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="347a9-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="347a9-815">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="347a9-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="347a9-816">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="347a9-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="347a9-817">Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="347a9-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="347a9-818">Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="347a9-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="347a9-819">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="347a9-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="347a9-820">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="347a9-821">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="347a9-822">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="347a9-822">MessagePack serialization options</span></span>

<span data-ttu-id="347a9-823">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="347a9-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="347a9-824">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="347a9-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-825">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="347a9-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="347a9-826">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="347a9-826">Configure server options</span></span>

<span data-ttu-id="347a9-827">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="347a9-828">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-828">Option</span></span> | <span data-ttu-id="347a9-829">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-829">Default Value</span></span> | <span data-ttu-id="347a9-830">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="347a9-831">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-831">15 seconds</span></span> | <span data-ttu-id="347a9-832">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="347a9-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="347a9-833">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-834">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="347a9-835">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-835">15 seconds</span></span> | <span data-ttu-id="347a9-836">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="347a9-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="347a9-837">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="347a9-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="347a9-838">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="347a9-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="347a9-839">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="347a9-839">All installed protocols</span></span> | <span data-ttu-id="347a9-840">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-840">Protocols supported by this hub.</span></span> <span data-ttu-id="347a9-841">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="347a9-842">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="347a9-843">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="347a9-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="347a9-844">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="347a9-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="347a9-845">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="347a9-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="347a9-846">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="347a9-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="347a9-847">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="347a9-848">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="347a9-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="347a9-849">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="347a9-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="347a9-850">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-850">Option</span></span> | <span data-ttu-id="347a9-851">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-851">Default Value</span></span> | <span data-ttu-id="347a9-852">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="347a9-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-853">32 KB</span></span> | <span data-ttu-id="347a9-854">Numero massimo di byte ricevuti dal client memorizzato nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="347a9-855">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="347a9-856">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="347a9-857">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="347a9-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="347a9-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="347a9-858">32 KB</span></span> | <span data-ttu-id="347a9-859">Numero massimo di byte inviati dall'app memorizzati nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="347a9-860">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="347a9-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="347a9-861">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="347a9-861">All Transports are enabled.</span></span> | <span data-ttu-id="347a9-862">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="347a9-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="347a9-863">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-863">See below.</span></span> | <span data-ttu-id="347a9-864">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="347a9-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="347a9-865">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="347a9-865">See below.</span></span> | <span data-ttu-id="347a9-866">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="347a9-867">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="347a9-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="347a9-868">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-868">Option</span></span> | <span data-ttu-id="347a9-869">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-869">Default Value</span></span> | <span data-ttu-id="347a9-870">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="347a9-871">90 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-871">90 seconds</span></span> | <span data-ttu-id="347a9-872">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="347a9-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="347a9-873">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="347a9-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="347a9-874">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="347a9-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="347a9-875">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-875">Option</span></span> | <span data-ttu-id="347a9-876">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-876">Default Value</span></span> | <span data-ttu-id="347a9-877">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="347a9-878">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-878">5 seconds</span></span> | <span data-ttu-id="347a9-879">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="347a9-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="347a9-880">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="347a9-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="347a9-881">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="347a9-882">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="347a9-882">Configure client options</span></span>

<span data-ttu-id="347a9-883">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="347a9-884">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="347a9-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="347a9-885">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="347a9-885">Configure logging</span></span>

<span data-ttu-id="347a9-886">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="347a9-887">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="347a9-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="347a9-888">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="347a9-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="347a9-889">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="347a9-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="347a9-890">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="347a9-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="347a9-891">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="347a9-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="347a9-892">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="347a9-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="347a9-893">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="347a9-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="347a9-894">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="347a9-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="347a9-895">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="347a9-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="347a9-896">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="347a9-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="347a9-897">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="347a9-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="347a9-898">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="347a9-899">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="347a9-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="347a9-900">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="347a9-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="347a9-901">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="347a9-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="347a9-902">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="347a9-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="347a9-903">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="347a9-903">Configure allowed transports</span></span>

<span data-ttu-id="347a9-904">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="347a9-905">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="347a9-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="347a9-906">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="347a9-906">All transports are enabled by default.</span></span>

<span data-ttu-id="347a9-907">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="347a9-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="347a9-908">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="347a9-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="347a9-909">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="347a9-909">Configure bearer authentication</span></span>

<span data-ttu-id="347a9-910">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="347a9-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="347a9-911">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="347a9-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="347a9-912">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="347a9-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="347a9-913">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="347a9-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="347a9-914">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="347a9-915">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="347a9-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="347a9-916">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="347a9-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="347a9-917">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="347a9-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="347a9-918">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="347a9-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="347a9-919">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="347a9-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="347a9-920">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="347a9-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-921">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-922">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-922">Option</span></span> | <span data-ttu-id="347a9-923">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-923">Default value</span></span> | <span data-ttu-id="347a9-924">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="347a9-925">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-926">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-926">Timeout for server activity.</span></span> <span data-ttu-id="347a9-927">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-928">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-929">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="347a9-930">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-930">15 seconds</span></span> | <span data-ttu-id="347a9-931">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-932">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="347a9-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="347a9-933">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-934">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="347a9-935">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="347a9-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="347a9-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-937">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-937">Option</span></span> | <span data-ttu-id="347a9-938">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-938">Default value</span></span> | <span data-ttu-id="347a9-939">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="347a9-940">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-941">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-941">Timeout for server activity.</span></span> <span data-ttu-id="347a9-942">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="347a9-943">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-944">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-945">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-945">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-946">Opzione</span><span class="sxs-lookup"><span data-stu-id="347a9-946">Option</span></span> | <span data-ttu-id="347a9-947">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-947">Default value</span></span> | <span data-ttu-id="347a9-948">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="347a9-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="347a9-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="347a9-950">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="347a9-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="347a9-951">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-951">Timeout for server activity.</span></span> <span data-ttu-id="347a9-952">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-953">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="347a9-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="347a9-954">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server, per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="347a9-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="347a9-955">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-955">15 seconds</span></span> | <span data-ttu-id="347a9-956">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="347a9-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="347a9-957">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="347a9-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="347a9-958">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="347a9-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="347a9-959">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="347a9-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="347a9-960">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-960">Configure additional options</span></span>

<span data-ttu-id="347a9-961">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="347a9-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="347a9-962">.NET</span><span class="sxs-lookup"><span data-stu-id="347a9-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="347a9-963">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="347a9-963">.NET Option</span></span> |  <span data-ttu-id="347a9-964">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="347a9-964">Default value</span></span> | <span data-ttu-id="347a9-965">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="347a9-966">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="347a9-967">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-968">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-969">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="347a9-970">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-970">Empty</span></span> | <span data-ttu-id="347a9-971">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="347a9-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="347a9-972">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-972">Empty</span></span> | <span data-ttu-id="347a9-973">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="347a9-974">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-974">Empty</span></span> | <span data-ttu-id="347a9-975">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="347a9-976">5 secondi</span><span class="sxs-lookup"><span data-stu-id="347a9-976">5 seconds</span></span> | <span data-ttu-id="347a9-977">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-977">WebSockets only.</span></span> <span data-ttu-id="347a9-978">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="347a9-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="347a9-979">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="347a9-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="347a9-980">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-980">Empty</span></span> | <span data-ttu-id="347a9-981">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="347a9-982">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="347a9-983">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="347a9-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="347a9-984">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="347a9-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="347a9-985">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="347a9-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="347a9-986">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="347a9-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="347a9-987">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="347a9-988">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="347a9-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="347a9-989">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="347a9-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="347a9-990">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="347a9-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="347a9-991">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="347a9-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="347a9-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="347a9-993">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="347a9-993">JavaScript Option</span></span> | <span data-ttu-id="347a9-994">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-994">Default Value</span></span> | <span data-ttu-id="347a9-995">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="347a9-996">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="347a9-997">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-998">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-999">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="347a9-1000">Java</span><span class="sxs-lookup"><span data-stu-id="347a9-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="347a9-1001">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="347a9-1001">Java Option</span></span> | <span data-ttu-id="347a9-1002">Default Value</span><span class="sxs-lookup"><span data-stu-id="347a9-1002">Default Value</span></span> | <span data-ttu-id="347a9-1003">Descrizione</span><span class="sxs-lookup"><span data-stu-id="347a9-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="347a9-1004">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="347a9-1005">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="347a9-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="347a9-1006">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="347a9-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="347a9-1007">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="347a9-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="347a9-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="347a9-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="347a9-1009">Empty</span><span class="sxs-lookup"><span data-stu-id="347a9-1009">Empty</span></span> | <span data-ttu-id="347a9-1010">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="347a9-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="347a9-1011">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="347a9-1012">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="347a9-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="347a9-1013">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="347a9-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="347a9-1014">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="347a9-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
