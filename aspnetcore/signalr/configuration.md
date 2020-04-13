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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223985"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="9060d-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9060d-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9060d-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9060d-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9060d-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9060d-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9060d-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol.JSON](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) serialization can be configured on the server using the AddJsonProtocol extension method.</span><span class="sxs-lookup"><span data-stu-id="9060d-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="9060d-108">`AddJsonProtocol`può essere aggiunto dopo `Startup.ConfigureServices` [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in .</span><span class="sxs-lookup"><span data-stu-id="9060d-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9060d-109">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="9060d-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9060d-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) di `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> tale oggetto è un oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9060d-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9060d-111">Per ulteriori informazioni, vedere la [documentazione di System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="9060d-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="9060d-112">Ad esempio, per configurare il serializzatore in modo da non modificare le maiuscole e le `Startup.ConfigureServices`minuscole dei nomi delle proprietà, anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in :</span><span class="sxs-lookup"><span data-stu-id="9060d-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="9060d-113">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9060d-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9060d-114">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9060d-115">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="9060d-116">Passare a Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="9060d-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="9060d-117">Se sono necessarie `Newtonsoft.Json` funzionalità non supportate in `System.Text.Json`, vedere Passare a [Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="9060d-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9060d-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-118">MessagePack serialization options</span></span>

<span data-ttu-id="9060d-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="9060d-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9060d-120">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="9060d-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-121">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9060d-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="9060d-122">Configure server options</span></span>

<span data-ttu-id="9060d-123">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9060d-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-124">Option</span></span> | <span data-ttu-id="9060d-125">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-125">Default Value</span></span> | <span data-ttu-id="9060d-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9060d-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-127">30 seconds</span></span> | <span data-ttu-id="9060d-128">Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9060d-129">Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9060d-130">Il valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="9060d-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9060d-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-131">15 seconds</span></span> | <span data-ttu-id="9060d-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="9060d-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9060d-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-134">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9060d-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-135">15 seconds</span></span> | <span data-ttu-id="9060d-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="9060d-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9060d-137">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="9060d-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9060d-138">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="9060d-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9060d-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="9060d-139">All installed protocols</span></span> | <span data-ttu-id="9060d-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-140">Protocols supported by this hub.</span></span> <span data-ttu-id="9060d-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9060d-142">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9060d-143">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="9060d-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="9060d-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="9060d-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="9060d-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi di flusso.</span><span class="sxs-lookup"><span data-stu-id="9060d-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="9060d-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-146">32 KB</span></span> | <span data-ttu-id="9060d-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="9060d-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="9060d-148">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="9060d-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9060d-149">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="9060d-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9060d-150">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="9060d-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9060d-151">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9060d-152">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9060d-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9060d-153">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9060d-154">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-154">Option</span></span> | <span data-ttu-id="9060d-155">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-155">Default Value</span></span> | <span data-ttu-id="9060d-156">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9060d-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-157">32 KB</span></span> | <span data-ttu-id="9060d-158">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la pressione arretrata.</span><span class="sxs-lookup"><span data-stu-id="9060d-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="9060d-159">L'aumento di questo valore consente al server di ricevere messaggi più grandi più rapidamente senza applicare la pressione a rito, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9060d-160">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9060d-161">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9060d-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-162">32 KB</span></span> | <span data-ttu-id="9060d-163">Numero massimo di byte inviati dall'app memorizzati nel buffer del server prima di osservare la retropressione.</span><span class="sxs-lookup"><span data-stu-id="9060d-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="9060d-164">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi più grandi più rapidamente senza attendere la pressione a riutilizzo, ma può aumentare il consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9060d-165">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="9060d-165">All Transports are enabled.</span></span> | <span data-ttu-id="9060d-166">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="9060d-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9060d-167">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-167">See below.</span></span> | <span data-ttu-id="9060d-168">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="9060d-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9060d-169">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-169">See below.</span></span> | <span data-ttu-id="9060d-170">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9060d-171">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="9060d-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9060d-172">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-172">Option</span></span> | <span data-ttu-id="9060d-173">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-173">Default Value</span></span> | <span data-ttu-id="9060d-174">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9060d-175">90 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-175">90 seconds</span></span> | <span data-ttu-id="9060d-176">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="9060d-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9060d-177">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="9060d-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9060d-178">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="9060d-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9060d-179">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-179">Option</span></span> | <span data-ttu-id="9060d-180">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-180">Default Value</span></span> | <span data-ttu-id="9060d-181">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9060d-182">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-182">5 seconds</span></span> | <span data-ttu-id="9060d-183">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="9060d-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9060d-184">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="9060d-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9060d-185">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9060d-186">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="9060d-186">Configure client options</span></span>

<span data-ttu-id="9060d-187">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9060d-188">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9060d-189">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="9060d-189">Configure logging</span></span>

<span data-ttu-id="9060d-190">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9060d-191">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="9060d-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9060d-192">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="9060d-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-193">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="9060d-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9060d-194">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="9060d-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9060d-195">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="9060d-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9060d-196">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9060d-197">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="9060d-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9060d-198">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="9060d-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9060d-199">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="9060d-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="9060d-200">Anziché `LogLevel` un valore, è `string` anche possibile fornire un valore che rappresenta un nome di livello di log.</span><span class="sxs-lookup"><span data-stu-id="9060d-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="9060d-201">Ciò è utile quando si configura la registrazione SignalR `LogLevel` in ambienti in cui non si ha accesso alle costanti.</span><span class="sxs-lookup"><span data-stu-id="9060d-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="9060d-202">Nella tabella seguente sono elencati i livelli di registrazione disponibili.</span><span class="sxs-lookup"><span data-stu-id="9060d-202">The following table lists the available log levels.</span></span> <span data-ttu-id="9060d-203">Il valore da `configureLogging` specificare per imposta il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="9060d-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="9060d-204">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo di esso nella tabella.**</span><span class="sxs-lookup"><span data-stu-id="9060d-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="9060d-205">string</span><span class="sxs-lookup"><span data-stu-id="9060d-205">String</span></span>                      | <span data-ttu-id="9060d-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="9060d-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="9060d-207">`info`**o o**`information`</span><span class="sxs-lookup"><span data-stu-id="9060d-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="9060d-208">`warn`**o o**`warning`</span><span class="sxs-lookup"><span data-stu-id="9060d-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="9060d-209">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9060d-210">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9060d-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9060d-211">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9060d-212">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="9060d-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9060d-213">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="9060d-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9060d-214">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="9060d-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9060d-215">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="9060d-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9060d-216">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="9060d-216">Configure allowed transports</span></span>

<span data-ttu-id="9060d-217">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9060d-218">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="9060d-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9060d-219">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9060d-219">All transports are enabled by default.</span></span>

<span data-ttu-id="9060d-220">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="9060d-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9060d-221">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="9060d-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9060d-222">In questa versione di Websockets client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="9060d-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="9060d-223">Nel client Java il trasporto viene `withTransport` selezionato `HttpHubConnectionBuilder`con il metodo sul metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="9060d-224">Per impostazione predefinita, il client Java utilizza il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9060d-225">Il client Java SignalR non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="9060d-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9060d-226">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="9060d-226">Configure bearer authentication</span></span>

<span data-ttu-id="9060d-227">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9060d-228">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="9060d-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9060d-229">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9060d-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9060d-230">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="9060d-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9060d-231">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9060d-232">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9060d-233">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9060d-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9060d-234">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9060d-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9060d-235">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="9060d-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9060d-236">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="9060d-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9060d-237">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="9060d-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-238">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-239">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-239">Option</span></span> | <span data-ttu-id="9060d-240">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-240">Default value</span></span> | <span data-ttu-id="9060d-241">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9060d-242">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-243">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-243">Timeout for server activity.</span></span> <span data-ttu-id="9060d-244">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-245">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-246">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9060d-247">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-247">15 seconds</span></span> | <span data-ttu-id="9060d-248">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-249">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-250">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-251">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9060d-252">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-252">15 seconds</span></span> | <span data-ttu-id="9060d-253">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-254">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-255">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9060d-256">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="9060d-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9060d-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-258">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-258">Option</span></span> | <span data-ttu-id="9060d-259">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-259">Default value</span></span> | <span data-ttu-id="9060d-260">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9060d-261">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-262">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-262">Timeout for server activity.</span></span> <span data-ttu-id="9060d-263">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9060d-264">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-265">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9060d-266">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9060d-267">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-268">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-269">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-270">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-270">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-271">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-271">Option</span></span> | <span data-ttu-id="9060d-272">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-272">Default value</span></span> | <span data-ttu-id="9060d-273">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9060d-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9060d-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9060d-275">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-276">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-276">Timeout for server activity.</span></span> <span data-ttu-id="9060d-277">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-278">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-279">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9060d-280">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-280">15 seconds</span></span> | <span data-ttu-id="9060d-281">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-282">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-283">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-284">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9060d-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9060d-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9060d-286">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9060d-287">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-288">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-289">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9060d-290">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-290">Configure additional options</span></span>

<span data-ttu-id="9060d-291">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="9060d-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-292">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-293">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="9060d-293">.NET Option</span></span> |  <span data-ttu-id="9060d-294">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-294">Default value</span></span> | <span data-ttu-id="9060d-295">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9060d-296">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9060d-297">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-298">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-299">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9060d-300">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-300">Empty</span></span> | <span data-ttu-id="9060d-301">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="9060d-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9060d-302">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-302">Empty</span></span> | <span data-ttu-id="9060d-303">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9060d-304">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-304">Empty</span></span> | <span data-ttu-id="9060d-305">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9060d-306">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-306">5 seconds</span></span> | <span data-ttu-id="9060d-307">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-307">WebSockets only.</span></span> <span data-ttu-id="9060d-308">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="9060d-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9060d-309">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="9060d-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9060d-310">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-310">Empty</span></span> | <span data-ttu-id="9060d-311">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9060d-312">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9060d-313">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9060d-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="9060d-314">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="9060d-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9060d-315">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="9060d-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9060d-316">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="9060d-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9060d-317">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9060d-318">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9060d-319">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="9060d-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9060d-320">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="9060d-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9060d-321">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="9060d-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9060d-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-323">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-323">JavaScript Option</span></span> | <span data-ttu-id="9060d-324">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-324">Default Value</span></span> | <span data-ttu-id="9060d-325">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9060d-326">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9060d-327">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-328">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-329">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-330">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-330">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-331">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="9060d-331">Java Option</span></span> | <span data-ttu-id="9060d-332">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-332">Default Value</span></span> | <span data-ttu-id="9060d-333">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9060d-334">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9060d-335">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-336">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-337">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9060d-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9060d-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9060d-339">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-339">Empty</span></span> | <span data-ttu-id="9060d-340">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9060d-341">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9060d-342">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9060d-343">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9060d-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9060d-344">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9060d-345">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9060d-346">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9060d-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9060d-347">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9060d-348">La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="9060d-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9060d-349">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="9060d-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9060d-350">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9060d-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9060d-351">Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9060d-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9060d-352">Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9060d-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9060d-353">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9060d-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9060d-354">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9060d-355">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9060d-356">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-356">MessagePack serialization options</span></span>

<span data-ttu-id="9060d-357">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="9060d-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9060d-358">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="9060d-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-359">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9060d-360">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="9060d-360">Configure server options</span></span>

<span data-ttu-id="9060d-361">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9060d-362">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-362">Option</span></span> | <span data-ttu-id="9060d-363">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-363">Default Value</span></span> | <span data-ttu-id="9060d-364">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="9060d-365">30 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-365">30 seconds</span></span> | <span data-ttu-id="9060d-366">Il server considererà il client disconnesso se non ha ricevuto un messaggio (incluso keep-alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="9060d-367">Potrebbe richiedere più tempo di questo intervallo di timeout per il client da contrassegnare effettivamente disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="9060d-368">Il valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="9060d-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="9060d-369">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-369">15 seconds</span></span> | <span data-ttu-id="9060d-370">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="9060d-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9060d-371">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-372">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9060d-373">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-373">15 seconds</span></span> | <span data-ttu-id="9060d-374">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="9060d-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9060d-375">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="9060d-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9060d-376">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="9060d-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9060d-377">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="9060d-377">All installed protocols</span></span> | <span data-ttu-id="9060d-378">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-378">Protocols supported by this hub.</span></span> <span data-ttu-id="9060d-379">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9060d-380">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9060d-381">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="9060d-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9060d-382">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="9060d-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9060d-383">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="9060d-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9060d-384">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="9060d-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9060d-385">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9060d-386">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9060d-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9060d-387">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9060d-388">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-388">Option</span></span> | <span data-ttu-id="9060d-389">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-389">Default Value</span></span> | <span data-ttu-id="9060d-390">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9060d-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-391">32 KB</span></span> | <span data-ttu-id="9060d-392">Numero massimo di byte ricevuti dal client memorizzato nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9060d-393">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9060d-394">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9060d-395">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9060d-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-396">32 KB</span></span> | <span data-ttu-id="9060d-397">Numero massimo di byte inviati dall'app memorizzati nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9060d-398">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9060d-399">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="9060d-399">All Transports are enabled.</span></span> | <span data-ttu-id="9060d-400">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="9060d-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9060d-401">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-401">See below.</span></span> | <span data-ttu-id="9060d-402">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="9060d-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9060d-403">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-403">See below.</span></span> | <span data-ttu-id="9060d-404">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9060d-405">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="9060d-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9060d-406">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-406">Option</span></span> | <span data-ttu-id="9060d-407">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-407">Default Value</span></span> | <span data-ttu-id="9060d-408">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9060d-409">90 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-409">90 seconds</span></span> | <span data-ttu-id="9060d-410">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="9060d-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9060d-411">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="9060d-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9060d-412">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="9060d-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9060d-413">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-413">Option</span></span> | <span data-ttu-id="9060d-414">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-414">Default Value</span></span> | <span data-ttu-id="9060d-415">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9060d-416">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-416">5 seconds</span></span> | <span data-ttu-id="9060d-417">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="9060d-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9060d-418">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="9060d-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9060d-419">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9060d-420">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="9060d-420">Configure client options</span></span>

<span data-ttu-id="9060d-421">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9060d-422">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9060d-423">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="9060d-423">Configure logging</span></span>

<span data-ttu-id="9060d-424">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9060d-425">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="9060d-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9060d-426">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="9060d-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-427">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="9060d-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9060d-428">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="9060d-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9060d-429">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="9060d-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9060d-430">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9060d-431">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="9060d-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9060d-432">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="9060d-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9060d-433">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="9060d-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9060d-434">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9060d-435">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9060d-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9060d-436">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9060d-437">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="9060d-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9060d-438">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="9060d-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9060d-439">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="9060d-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9060d-440">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="9060d-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9060d-441">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="9060d-441">Configure allowed transports</span></span>

<span data-ttu-id="9060d-442">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9060d-443">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="9060d-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9060d-444">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9060d-444">All transports are enabled by default.</span></span>

<span data-ttu-id="9060d-445">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="9060d-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9060d-446">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="9060d-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="9060d-447">In questa versione di Websockets client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="9060d-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9060d-448">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="9060d-448">Configure bearer authentication</span></span>

<span data-ttu-id="9060d-449">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9060d-450">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="9060d-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9060d-451">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9060d-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9060d-452">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="9060d-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9060d-453">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9060d-454">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9060d-455">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9060d-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9060d-456">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9060d-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9060d-457">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="9060d-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9060d-458">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="9060d-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9060d-459">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="9060d-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-460">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-461">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-461">Option</span></span> | <span data-ttu-id="9060d-462">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-462">Default value</span></span> | <span data-ttu-id="9060d-463">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9060d-464">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-465">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-465">Timeout for server activity.</span></span> <span data-ttu-id="9060d-466">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-467">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-468">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9060d-469">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-469">15 seconds</span></span> | <span data-ttu-id="9060d-470">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-471">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-472">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-473">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9060d-474">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-474">15 seconds</span></span> | <span data-ttu-id="9060d-475">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-476">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-477">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="9060d-478">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="9060d-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9060d-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-480">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-480">Option</span></span> | <span data-ttu-id="9060d-481">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-481">Default value</span></span> | <span data-ttu-id="9060d-482">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9060d-483">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-484">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-484">Timeout for server activity.</span></span> <span data-ttu-id="9060d-485">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9060d-486">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-487">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="9060d-488">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9060d-489">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-490">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-491">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-492">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-492">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-493">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-493">Option</span></span> | <span data-ttu-id="9060d-494">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-494">Default value</span></span> | <span data-ttu-id="9060d-495">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9060d-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9060d-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9060d-497">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-498">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-498">Timeout for server activity.</span></span> <span data-ttu-id="9060d-499">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-500">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-501">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9060d-502">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-502">15 seconds</span></span> | <span data-ttu-id="9060d-503">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-504">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-505">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-506">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="9060d-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="9060d-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="9060d-508">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="9060d-509">Determina l'intervallo in cui il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="9060d-510">L'invio di qualsiasi messaggio dal client reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="9060d-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="9060d-511">Se il client non ha inviato `ClientTimeoutInterval` un messaggio nel set sul server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9060d-512">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-512">Configure additional options</span></span>

<span data-ttu-id="9060d-513">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="9060d-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-514">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-515">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="9060d-515">.NET Option</span></span> |  <span data-ttu-id="9060d-516">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-516">Default value</span></span> | <span data-ttu-id="9060d-517">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9060d-518">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9060d-519">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-520">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-521">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9060d-522">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-522">Empty</span></span> | <span data-ttu-id="9060d-523">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="9060d-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9060d-524">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-524">Empty</span></span> | <span data-ttu-id="9060d-525">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9060d-526">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-526">Empty</span></span> | <span data-ttu-id="9060d-527">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9060d-528">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-528">5 seconds</span></span> | <span data-ttu-id="9060d-529">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-529">WebSockets only.</span></span> <span data-ttu-id="9060d-530">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="9060d-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9060d-531">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="9060d-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9060d-532">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-532">Empty</span></span> | <span data-ttu-id="9060d-533">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9060d-534">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9060d-535">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9060d-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="9060d-536">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="9060d-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9060d-537">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="9060d-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9060d-538">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="9060d-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9060d-539">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9060d-540">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9060d-541">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="9060d-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9060d-542">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="9060d-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9060d-543">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="9060d-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9060d-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-545">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-545">JavaScript Option</span></span> | <span data-ttu-id="9060d-546">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-546">Default Value</span></span> | <span data-ttu-id="9060d-547">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9060d-548">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9060d-549">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-550">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-551">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-552">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-552">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-553">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="9060d-553">Java Option</span></span> | <span data-ttu-id="9060d-554">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-554">Default Value</span></span> | <span data-ttu-id="9060d-555">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9060d-556">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9060d-557">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-558">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-559">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9060d-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9060d-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9060d-561">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-561">Empty</span></span> | <span data-ttu-id="9060d-562">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9060d-563">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9060d-564">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9060d-565">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9060d-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9060d-566">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="9060d-567">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="9060d-568">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="9060d-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="9060d-569">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="9060d-570">La serializzazione JSON può essere configurata nel server usando il metodo di `Startup.ConfigureServices` estensione [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) che può essere aggiunto dopo [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel metodo.</span><span class="sxs-lookup"><span data-stu-id="9060d-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9060d-571">Il `AddJsonProtocol` metodo accetta un delegato `options` che riceve un oggetto.</span><span class="sxs-lookup"><span data-stu-id="9060d-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="9060d-572">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) di tale `JsonSerializerSettings` oggetto è un oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="9060d-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="9060d-573">Per ulteriori informazioni, vedere la [documentazione relativa alla JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="9060d-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="9060d-574">Ad esempio, per configurare il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9060d-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="9060d-575">Nel client .NET lo `AddJsonProtocol` stesso metodo di estensione esiste in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="9060d-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="9060d-576">Lo `Microsoft.Extensions.DependencyInjection` spazio dei nomi deve essere importato per risolvere il metodo di estensione:The namespace must be imported to resolve the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="9060d-577">Non è possibile configurare la serializzazione JSON nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="9060d-578">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="9060d-578">MessagePack serialization options</span></span>

<span data-ttu-id="9060d-579">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol.MessagePack](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) serialization can be configured by providing a delegate to the AddMessagePackProtocol call.</span><span class="sxs-lookup"><span data-stu-id="9060d-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="9060d-580">Vedere [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) per ulteriori dettagli.</span><span class="sxs-lookup"><span data-stu-id="9060d-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-581">Non è possibile configurare la serializzazione MessagePack nel client JavaScript in questo momento.</span><span class="sxs-lookup"><span data-stu-id="9060d-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="9060d-582">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="9060d-582">Configure server options</span></span>

<span data-ttu-id="9060d-583">Nella tabella seguente vengono descritte le opzioni per la configurazione degli hub SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="9060d-584">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-584">Option</span></span> | <span data-ttu-id="9060d-585">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-585">Default Value</span></span> | <span data-ttu-id="9060d-586">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="9060d-587">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-587">15 seconds</span></span> | <span data-ttu-id="9060d-588">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="9060d-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="9060d-589">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-590">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="9060d-591">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-591">15 seconds</span></span> | <span data-ttu-id="9060d-592">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio ping per mantenere aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="9060d-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="9060d-593">Quando `KeepAliveInterval`si modifica `ServerTimeout` / `serverTimeoutInMilliseconds` , modificare l'impostazione sul client.</span><span class="sxs-lookup"><span data-stu-id="9060d-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="9060d-594">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato `KeepAliveInterval` è double del valore.</span><span class="sxs-lookup"><span data-stu-id="9060d-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="9060d-595">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="9060d-595">All installed protocols</span></span> | <span data-ttu-id="9060d-596">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-596">Protocols supported by this hub.</span></span> <span data-ttu-id="9060d-597">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="9060d-598">Se `true`, i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="9060d-599">Il valore `false`predefinito è , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="9060d-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="9060d-600">Le opzioni possono essere configurate per tutti `AddSignalR` gli `Startup.ConfigureServices`hub fornendo un delegato delle opzioni alla chiamata in .</span><span class="sxs-lookup"><span data-stu-id="9060d-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="9060d-601">Le opzioni per un singolo hub `AddSignalR` sostituiscono le <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>opzioni globali disponibili in e possono essere configurate utilizzando:</span><span class="sxs-lookup"><span data-stu-id="9060d-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="9060d-602">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="9060d-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="9060d-603">Consente `HttpConnectionDispatcherOptions` di configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="9060d-604">Queste opzioni vengono configurate passando un delegato `Startup.Configure`a [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in .</span><span class="sxs-lookup"><span data-stu-id="9060d-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="9060d-605">Nella tabella seguente vengono descritte le opzioni per la configurazione delle opzioni HTTP avanzate di ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="9060d-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="9060d-606">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-606">Option</span></span> | <span data-ttu-id="9060d-607">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-607">Default Value</span></span> | <span data-ttu-id="9060d-608">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="9060d-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-609">32 KB</span></span> | <span data-ttu-id="9060d-610">Numero massimo di byte ricevuti dal client memorizzato nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="9060d-611">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="9060d-612">Dati raccolti automaticamente `Authorize` dagli attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="9060d-613">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) utilizzati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="9060d-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="9060d-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="9060d-614">32 KB</span></span> | <span data-ttu-id="9060d-615">Numero massimo di byte inviati dall'app memorizzati nel buffer del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="9060d-616">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sul consumo di memoria.</span><span class="sxs-lookup"><span data-stu-id="9060d-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="9060d-617">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="9060d-617">All Transports are enabled.</span></span> | <span data-ttu-id="9060d-618">Contrassegni di bit `HttpTransportType` enum di valori che possono limitare i trasporti che un client può utilizzare per connettersi.</span><span class="sxs-lookup"><span data-stu-id="9060d-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="9060d-619">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-619">See below.</span></span> | <span data-ttu-id="9060d-620">Opzioni aggiuntive specifiche per il trasporto Polling lungo.</span><span class="sxs-lookup"><span data-stu-id="9060d-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="9060d-621">Vedere di seguito.</span><span class="sxs-lookup"><span data-stu-id="9060d-621">See below.</span></span> | <span data-ttu-id="9060d-622">Opzioni aggiuntive specifiche per il trasporto WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="9060d-623">Il trasporto Polling lungo include opzioni aggiuntive `LongPolling` che possono essere configurate utilizzando la proprietà:The Long Polling transport has additional options that can be configured using the property:</span><span class="sxs-lookup"><span data-stu-id="9060d-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="9060d-624">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-624">Option</span></span> | <span data-ttu-id="9060d-625">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-625">Default Value</span></span> | <span data-ttu-id="9060d-626">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="9060d-627">90 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-627">90 seconds</span></span> | <span data-ttu-id="9060d-628">Il tempo massimo di attesa del server per l'invio di un messaggio al client prima di terminare una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="9060d-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="9060d-629">La riduzione di questo valore fa sì che il client emetta nuove richieste di polling con maggiore frequenza.</span><span class="sxs-lookup"><span data-stu-id="9060d-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="9060d-630">Il trasporto WebSocket dispone di opzioni `WebSockets` aggiuntive che possono essere configurate utilizzando la proprietà:</span><span class="sxs-lookup"><span data-stu-id="9060d-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="9060d-631">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-631">Option</span></span> | <span data-ttu-id="9060d-632">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-632">Default Value</span></span> | <span data-ttu-id="9060d-633">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="9060d-634">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-634">5 seconds</span></span> | <span data-ttu-id="9060d-635">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="9060d-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="9060d-636">Delegato che può essere utilizzato `Sec-WebSocket-Protocol` per impostare l'intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="9060d-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="9060d-637">Il delegato riceve i valori richiesti dal client come input e deve restituire il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="9060d-638">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="9060d-638">Configure client options</span></span>

<span data-ttu-id="9060d-639">Le opzioni client possono `HubConnectionBuilder` essere configurate sul tipo (disponibile nei client .NET e JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="9060d-640">È disponibile anche nel client Java, `HttpHubConnectionBuilder` ma la sottoclasse è ciò che `HubConnection` contiene le opzioni di configurazione del generatore, così come su se stesso.</span><span class="sxs-lookup"><span data-stu-id="9060d-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="9060d-641">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="9060d-641">Configure logging</span></span>

<span data-ttu-id="9060d-642">La registrazione viene configurata nel `ConfigureLogging` client .NET utilizzando il metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="9060d-643">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano sul server.</span><span class="sxs-lookup"><span data-stu-id="9060d-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="9060d-644">Per altre informazioni, vedere la documentazione [Logging in ASP.NET Core.See](xref:fundamentals/logging/index) the Logging in ASP.NET Core documentation for more information.</span><span class="sxs-lookup"><span data-stu-id="9060d-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="9060d-645">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="9060d-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="9060d-646">Vedere la sezione Provider di [registrazione predefiniti](xref:fundamentals/logging/index#built-in-logging-providers) della documentazione per un elenco completo.</span><span class="sxs-lookup"><span data-stu-id="9060d-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="9060d-647">Ad esempio, per abilitare `Microsoft.Extensions.Logging.Console` la registrazione della console, installare il pacchetto NuGet.For example, to enable Console logging, install the NuGet package.</span><span class="sxs-lookup"><span data-stu-id="9060d-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="9060d-648">Chiamare `AddConsole` il metodo di estensione:Call the extension method:</span><span class="sxs-lookup"><span data-stu-id="9060d-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="9060d-649">Nel client JavaScript esiste `configureLogging` un metodo simile.</span><span class="sxs-lookup"><span data-stu-id="9060d-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="9060d-650">Specificare `LogLevel` un valore che indica il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="9060d-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="9060d-651">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="9060d-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="9060d-652">Per disabilitare completamente `signalR.LogLevel.None` la `configureLogging` registrazione, specificare nel metodo .</span><span class="sxs-lookup"><span data-stu-id="9060d-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="9060d-653">Per ulteriori informazioni sulla registrazione, vedere la documentazione di [SignalR Diagnostics](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="9060d-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="9060d-654">Il client Java SignalR utilizza la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="9060d-655">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere la propria implementazione di registrazione specifica introducendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="9060d-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="9060d-656">Il frammento di codice `java.util.logging` seguente viene illustrato come utilizzare con il client Java SignalR.The following code snippet shows how to use with the SignalR Java client.</span><span class="sxs-lookup"><span data-stu-id="9060d-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="9060d-657">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger di non operazione predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="9060d-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="9060d-658">Questo può essere tranquillamente ignorato.</span><span class="sxs-lookup"><span data-stu-id="9060d-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="9060d-659">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="9060d-659">Configure allowed transports</span></span>

<span data-ttu-id="9060d-660">I trasporti utilizzati da SignalR `WithUrl` possono`withUrl` essere configurati nella chiamata (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="9060d-661">Un OR bit per bit `HttpTransportType` dei valori di può essere utilizzato per limitare il client per utilizzare solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="9060d-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="9060d-662">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="9060d-662">All transports are enabled by default.</span></span>

<span data-ttu-id="9060d-663">Ad esempio, per disabilitare il trasporto di eventi Server-Sent, ma consentire le connessioni WebSockets e Long Polling:</span><span class="sxs-lookup"><span data-stu-id="9060d-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="9060d-664">Nel client JavaScript, i trasporti vengono `transport` configurati impostando il `withUrl`campo nell'oggetto options fornito su :</span><span class="sxs-lookup"><span data-stu-id="9060d-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="9060d-665">Configurare l'autenticazione del portatore</span><span class="sxs-lookup"><span data-stu-id="9060d-665">Configure bearer authentication</span></span>

<span data-ttu-id="9060d-666">Per fornire dati di autenticazione insieme `AccessTokenProvider` alle`accessTokenFactory` richieste SignalR, utilizzare l'opzione ( in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="9060d-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="9060d-667">Nel client .NET, questo token di accesso viene passato come token `Authorization` HTTP "Bearer `Bearer`Authentication" (utilizzando l'intestazione con un tipo di ).</span><span class="sxs-lookup"><span data-stu-id="9060d-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="9060d-668">Nel client JavaScript, il token di accesso viene usato come token Bearer, **tranne** in alcuni casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste Server-Sent Events e WebSockets).</span><span class="sxs-lookup"><span data-stu-id="9060d-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="9060d-669">In questi casi, il token di accesso `access_token`viene fornito come valore della stringa di query.</span><span class="sxs-lookup"><span data-stu-id="9060d-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="9060d-670">Nel client .NET, `AccessTokenProvider` l'opzione può essere `WithUrl`specificata utilizzando il delegato options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="9060d-671">Nel client JavaScript, il token di `accessTokenFactory` accesso viene configurato `withUrl`impostando il campo sull'oggetto options in :</span><span class="sxs-lookup"><span data-stu-id="9060d-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="9060d-672">Nel client Java SignalR è possibile configurare un token di connessione da utilizzare per l'autenticazione fornendo una factory di token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="9060d-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="9060d-673">Utilizzare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire una [stringa singola\< ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava)>.</span><span class="sxs-lookup"><span data-stu-id="9060d-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="9060d-674">Con una chiamata a [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="9060d-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="9060d-675">Configurare le opzioni di timeout e keep-alive</span><span class="sxs-lookup"><span data-stu-id="9060d-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="9060d-676">Sull'oggetto `HubConnection` stesso sono disponibili opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive:</span><span class="sxs-lookup"><span data-stu-id="9060d-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-677">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-678">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-678">Option</span></span> | <span data-ttu-id="9060d-679">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-679">Default value</span></span> | <span data-ttu-id="9060d-680">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="9060d-681">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-682">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-682">Timeout for server activity.</span></span> <span data-ttu-id="9060d-683">Se il server non ha inviato un messaggio in questo intervallo, `Closed` il`onclose` client considera il server disconnesso e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-684">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-685">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="9060d-686">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-686">15 seconds</span></span> | <span data-ttu-id="9060d-687">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-688">Se il server non invia una risposta di handshake in questo intervallo, `Closed` il`onclose` client annulla l'handshake e attiva l'evento (in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9060d-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="9060d-689">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-690">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="9060d-691">Nel client .NET i valori `TimeSpan` di timeout vengono specificati come valori.</span><span class="sxs-lookup"><span data-stu-id="9060d-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="9060d-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-693">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-693">Option</span></span> | <span data-ttu-id="9060d-694">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-694">Default value</span></span> | <span data-ttu-id="9060d-695">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="9060d-696">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-697">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-697">Timeout for server activity.</span></span> <span data-ttu-id="9060d-698">Se il server non ha inviato un messaggio in questo intervallo, `onclose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="9060d-699">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-700">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-701">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-701">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-702">Opzione</span><span class="sxs-lookup"><span data-stu-id="9060d-702">Option</span></span> | <span data-ttu-id="9060d-703">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-703">Default value</span></span> | <span data-ttu-id="9060d-704">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="9060d-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="9060d-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="9060d-706">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="9060d-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="9060d-707">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-707">Timeout for server activity.</span></span> <span data-ttu-id="9060d-708">Se il server non ha inviato un messaggio in questo intervallo, `onClose` il client considera il server disconnesso e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-709">Questo valore deve essere sufficientemente grande per poter inviare un messaggio ping dal server **e** ricevere dal client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="9060d-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="9060d-710">Il valore consigliato è un numero almeno `KeepAliveInterval` il doppio del valore del server, per consentire l'arrivo di ping.</span><span class="sxs-lookup"><span data-stu-id="9060d-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="9060d-711">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-711">15 seconds</span></span> | <span data-ttu-id="9060d-712">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="9060d-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="9060d-713">Se il server non invia una risposta di handshake in questo intervallo, `onClose` il client annulla l'handshake e attiva l'evento.</span><span class="sxs-lookup"><span data-stu-id="9060d-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="9060d-714">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="9060d-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="9060d-715">Per ulteriori dettagli sul processo di handshake, vedere [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="9060d-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="9060d-716">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-716">Configure additional options</span></span>

<span data-ttu-id="9060d-717">È possibile configurare `WithUrl` opzioni`withUrl` aggiuntive nel metodo `HubConnectionBuilder` (in JavaScript) su `HttpHubConnectionBuilder` o sulle varie API di configurazione nel client Java:</span><span class="sxs-lookup"><span data-stu-id="9060d-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="9060d-718">.NET</span><span class="sxs-lookup"><span data-stu-id="9060d-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="9060d-719">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="9060d-719">.NET Option</span></span> |  <span data-ttu-id="9060d-720">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="9060d-720">Default value</span></span> | <span data-ttu-id="9060d-721">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="9060d-722">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="9060d-723">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-724">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-725">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="9060d-726">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-726">Empty</span></span> | <span data-ttu-id="9060d-727">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="9060d-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="9060d-728">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-728">Empty</span></span> | <span data-ttu-id="9060d-729">Raccolta di cookie HTTP da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="9060d-730">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-730">Empty</span></span> | <span data-ttu-id="9060d-731">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="9060d-732">5 secondi</span><span class="sxs-lookup"><span data-stu-id="9060d-732">5 seconds</span></span> | <span data-ttu-id="9060d-733">Solo WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-733">WebSockets only.</span></span> <span data-ttu-id="9060d-734">Il tempo massimo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="9060d-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="9060d-735">Se il server non riconosce la chiusura entro questo periodo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="9060d-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="9060d-736">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-736">Empty</span></span> | <span data-ttu-id="9060d-737">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="9060d-738">Delegato che può essere utilizzato per `HttpMessageHandler` configurare o sostituire l'oggetto utilizzato per inviare richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="9060d-739">Non utilizzato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9060d-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="9060d-740">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="9060d-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="9060d-741">Modificare le impostazioni su tale valore predefinito e `HttpMessageHandler` restituirlo oppure restituire una nuova istanza.</span><span class="sxs-lookup"><span data-stu-id="9060d-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="9060d-742">**Quando si sostituisce il gestore assicurarsi di copiare le impostazioni che si desidera mantenere dal gestore fornito, in caso contrario, le opzioni configurate (ad esempio cookie e intestazioni) non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="9060d-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="9060d-743">Un proxy HTTP da utilizzare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="9060d-744">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9060d-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="9060d-745">Ciò consente l'utilizzo dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="9060d-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="9060d-746">Delegato che può essere utilizzato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="9060d-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="9060d-747">Riceve un'istanza di [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="9060d-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="9060d-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="9060d-749">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="9060d-749">JavaScript Option</span></span> | <span data-ttu-id="9060d-750">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-750">Default Value</span></span> | <span data-ttu-id="9060d-751">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="9060d-752">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="9060d-753">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-754">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-755">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="9060d-756">Java</span><span class="sxs-lookup"><span data-stu-id="9060d-756">Java</span></span>](#tab/java)

| <span data-ttu-id="9060d-757">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="9060d-757">Java Option</span></span> | <span data-ttu-id="9060d-758">Default Value</span><span class="sxs-lookup"><span data-stu-id="9060d-758">Default Value</span></span> | <span data-ttu-id="9060d-759">Descrizione</span><span class="sxs-lookup"><span data-stu-id="9060d-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="9060d-760">Funzione che restituisce una stringa fornita come token di autenticazione Bearer nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="9060d-761">Impostare `true` questa opzione su per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="9060d-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="9060d-762">**Supportato solo quando il trasporto WebSockets è l'unico trasporto abilitato.**</span><span class="sxs-lookup"><span data-stu-id="9060d-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="9060d-763">Questa impostazione non può essere abilitata quando si usa il servizio SignalR di Azure.This setting can't be enabled when using the Azure SignalR Service.</span><span class="sxs-lookup"><span data-stu-id="9060d-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="9060d-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="9060d-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="9060d-765">Empty</span><span class="sxs-lookup"><span data-stu-id="9060d-765">Empty</span></span> | <span data-ttu-id="9060d-766">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="9060d-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="9060d-767">Nel client .NET queste opzioni possono essere modificate `WithUrl`dal delegato options fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="9060d-768">Nel client JavaScript, queste opzioni possono essere fornite `withUrl`in un oggetto JavaScript fornito a :</span><span class="sxs-lookup"><span data-stu-id="9060d-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="9060d-769">Nel client Java, queste opzioni possono essere `HttpHubConnectionBuilder` configurate con i metodi`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="9060d-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="9060d-770">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="9060d-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
