---
title: Configurazione di ASP.NET Core SignalR
author: bradygaster
description: Informazioni su come configurare ASP.NET Core SignalR app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393873"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="d5538-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d5538-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5538-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5538-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5538-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5538-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5538-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5538-108">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5538-109">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d5538-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5538-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="d5538-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5538-111">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5538-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5538-112">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5538-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d5538-113">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5538-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5538-114">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="d5538-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5538-115">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5538-116">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d5538-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5538-117">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5538-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5538-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-118">MessagePack serialization options</span></span>

<span data-ttu-id="d5538-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5538-120">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-121">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5538-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="d5538-122">Configure server options</span></span>

<span data-ttu-id="d5538-123">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="d5538-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5538-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-124">Option</span></span> | <span data-ttu-id="d5538-125">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-125">Default Value</span></span> | <span data-ttu-id="d5538-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5538-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-127">30 seconds</span></span> | <span data-ttu-id="d5538-128">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5538-129">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5538-130">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5538-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-131">15 seconds</span></span> | <span data-ttu-id="d5538-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="d5538-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5538-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-134">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-135">15 seconds</span></span> | <span data-ttu-id="d5538-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5538-137">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="d5538-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5538-138">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5538-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="d5538-139">All installed protocols</span></span> | <span data-ttu-id="d5538-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-140">Protocols supported by this hub.</span></span> <span data-ttu-id="d5538-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5538-142">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5538-143">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="d5538-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5538-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="d5538-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5538-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="d5538-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5538-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-146">32 KB</span></span> | <span data-ttu-id="d5538-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="d5538-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="d5538-148">1</span><span class="sxs-lookup"><span data-stu-id="d5538-148">1</span></span> | <span data-ttu-id="d5538-149">Numero massimo di metodi dell'hub che ogni client può chiamare in parallelo prima della coda.</span><span class="sxs-lookup"><span data-stu-id="d5538-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="d5538-150">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5538-151">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5538-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5538-152">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="d5538-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5538-153">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5538-154">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5538-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5538-155">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5538-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5538-156">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-156">Option</span></span> | <span data-ttu-id="d5538-157">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-157">Default Value</span></span> | <span data-ttu-id="d5538-158">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5538-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-159">32 KB</span></span> | <span data-ttu-id="d5538-160">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5538-161">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5538-162">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5538-163">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5538-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-164">32 KB</span></span> | <span data-ttu-id="d5538-165">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5538-166">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5538-167">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="d5538-167">All Transports are enabled.</span></span> | <span data-ttu-id="d5538-168">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5538-169">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-169">See below.</span></span> | <span data-ttu-id="d5538-170">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="d5538-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5538-171">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-171">See below.</span></span> | <span data-ttu-id="d5538-172">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d5538-173">0</span><span class="sxs-lookup"><span data-stu-id="d5538-173">0</span></span> | <span data-ttu-id="d5538-174">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d5538-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d5538-175">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="d5538-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d5538-176">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5538-177">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-177">Option</span></span> | <span data-ttu-id="d5538-178">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-178">Default Value</span></span> | <span data-ttu-id="d5538-179">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5538-180">90 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-180">90 seconds</span></span> | <span data-ttu-id="d5538-181">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5538-182">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5538-183">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5538-184">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-184">Option</span></span> | <span data-ttu-id="d5538-185">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-185">Default Value</span></span> | <span data-ttu-id="d5538-186">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5538-187">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-187">5 seconds</span></span> | <span data-ttu-id="d5538-188">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="d5538-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5538-189">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d5538-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5538-190">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5538-191">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="d5538-191">Configure client options</span></span>

<span data-ttu-id="d5538-192">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5538-193">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5538-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5538-194">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="d5538-194">Configure logging</span></span>

<span data-ttu-id="d5538-195">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5538-196">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="d5538-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5538-197">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="d5538-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-198">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="d5538-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5538-199">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="d5538-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5538-200">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5538-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5538-201">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d5538-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5538-202">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="d5538-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5538-203">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="d5538-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5538-204">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="d5538-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5538-205">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="d5538-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5538-206">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="d5538-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5538-207">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="d5538-207">The following table lists the available log levels.</span></span> <span data-ttu-id="d5538-208">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="d5538-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5538-209">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="d5538-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5538-210">string</span><span class="sxs-lookup"><span data-stu-id="d5538-210">String</span></span>                      | <span data-ttu-id="d5538-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5538-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5538-212">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="d5538-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5538-213">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5538-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5538-214">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5538-215">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5538-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5538-216">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5538-217">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="d5538-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5538-218">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="d5538-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5538-219">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="d5538-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5538-220">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="d5538-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5538-221">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="d5538-221">Configure allowed transports</span></span>

<span data-ttu-id="d5538-222">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5538-223">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="d5538-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5538-224">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d5538-224">All transports are enabled by default.</span></span>

<span data-ttu-id="d5538-225">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="d5538-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5538-226">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5538-227">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="d5538-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5538-228">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5538-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5538-229">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5538-230">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="d5538-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5538-231">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="d5538-231">Configure bearer authentication</span></span>

<span data-ttu-id="d5538-232">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5538-233">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5538-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5538-234">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5538-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5538-235">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5538-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5538-236">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5538-237">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5538-238">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5538-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5538-239">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="d5538-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5538-240">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="d5538-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5538-241">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5538-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5538-242">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="d5538-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-243">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-244">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-244">Option</span></span> | <span data-ttu-id="d5538-245">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-245">Default value</span></span> | <span data-ttu-id="d5538-246">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5538-247">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-248">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-248">Timeout for server activity.</span></span> <span data-ttu-id="d5538-249">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-250">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-251">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5538-252">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-252">15 seconds</span></span> | <span data-ttu-id="d5538-253">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-254">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-255">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-256">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-257">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-257">15 seconds</span></span> | <span data-ttu-id="d5538-258">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-259">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-260">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5538-261">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="d5538-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5538-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-263">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-263">Option</span></span> | <span data-ttu-id="d5538-264">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-264">Default value</span></span> | <span data-ttu-id="d5538-265">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5538-266">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-267">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-267">Timeout for server activity.</span></span> <span data-ttu-id="d5538-268">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5538-269">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-270">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5538-271">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-272">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-273">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-274">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-275">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-275">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-276">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-276">Option</span></span> | <span data-ttu-id="d5538-277">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-277">Default value</span></span> | <span data-ttu-id="d5538-278">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5538-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5538-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5538-280">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-281">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-281">Timeout for server activity.</span></span> <span data-ttu-id="d5538-282">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-283">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-284">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5538-285">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-285">15 seconds</span></span> | <span data-ttu-id="d5538-286">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-287">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-288">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-289">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5538-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5538-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5538-291">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-292">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-293">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-294">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5538-295">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-295">Configure additional options</span></span>

<span data-ttu-id="d5538-296">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="d5538-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-297">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-298">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="d5538-298">.NET Option</span></span> |  <span data-ttu-id="d5538-299">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-299">Default value</span></span> | <span data-ttu-id="d5538-300">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5538-301">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5538-302">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-303">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-304">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5538-305">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-305">Empty</span></span> | <span data-ttu-id="d5538-306">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="d5538-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5538-307">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-307">Empty</span></span> | <span data-ttu-id="d5538-308">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5538-309">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-309">Empty</span></span> | <span data-ttu-id="d5538-310">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5538-311">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-311">5 seconds</span></span> | <span data-ttu-id="d5538-312">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-312">WebSockets only.</span></span> <span data-ttu-id="d5538-313">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="d5538-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5538-314">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="d5538-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5538-315">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-315">Empty</span></span> | <span data-ttu-id="d5538-316">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5538-317">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5538-318">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5538-319">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="d5538-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5538-320">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="d5538-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5538-321">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="d5538-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5538-322">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5538-323">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5538-324">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="d5538-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5538-325">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d5538-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5538-326">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d5538-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5538-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-328">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-328">JavaScript Option</span></span> | <span data-ttu-id="d5538-329">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-329">Default Value</span></span> | <span data-ttu-id="d5538-330">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5538-331">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="d5538-332">Dizionario di intestazioni inviate con tutte le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="d5538-333">L'invio di intestazioni nel browser non funziona per WebSocket o per il <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flusso.</span><span class="sxs-lookup"><span data-stu-id="d5538-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5538-334">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="d5538-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5538-335">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-336">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-337">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="d5538-338">Specifica se le credenziali verranno inviate con la richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="d5538-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="d5538-339">App Azure servizio usa cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per il corretto funzionamento.</span><span class="sxs-lookup"><span data-stu-id="d5538-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="d5538-340">Per ulteriori informazioni su CORS con SignalR , vedere <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="d5538-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-341">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-341">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-342">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="d5538-342">Java Option</span></span> | <span data-ttu-id="d5538-343">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-343">Default Value</span></span> | <span data-ttu-id="d5538-344">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5538-345">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5538-346">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-347">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-348">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5538-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5538-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5538-350">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-350">Empty</span></span> | <span data-ttu-id="d5538-351">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5538-352">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5538-353">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5538-354">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5538-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5538-355">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5538-356">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5538-357">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5538-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5538-358">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5538-359">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5538-360">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5538-361">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d5538-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5538-362">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="d5538-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5538-363">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5538-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5538-364">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5538-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="d5538-365">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5538-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5538-366">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="d5538-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5538-367">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5538-368">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d5538-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5538-369">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5538-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5538-370">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-370">MessagePack serialization options</span></span>

<span data-ttu-id="d5538-371">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5538-372">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-373">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5538-374">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="d5538-374">Configure server options</span></span>

<span data-ttu-id="d5538-375">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="d5538-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5538-376">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-376">Option</span></span> | <span data-ttu-id="d5538-377">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-377">Default Value</span></span> | <span data-ttu-id="d5538-378">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5538-379">30 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-379">30 seconds</span></span> | <span data-ttu-id="d5538-380">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5538-381">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5538-382">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5538-383">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-383">15 seconds</span></span> | <span data-ttu-id="d5538-384">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="d5538-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5538-385">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-386">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-387">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-387">15 seconds</span></span> | <span data-ttu-id="d5538-388">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5538-389">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="d5538-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5538-390">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5538-391">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="d5538-391">All installed protocols</span></span> | <span data-ttu-id="d5538-392">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-392">Protocols supported by this hub.</span></span> <span data-ttu-id="d5538-393">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5538-394">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5538-395">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="d5538-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5538-396">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="d5538-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5538-397">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="d5538-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5538-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-398">32 KB</span></span> | <span data-ttu-id="d5538-399">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="d5538-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5538-400">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5538-401">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5538-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5538-402">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="d5538-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5538-403">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5538-404">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5538-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5538-405">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5538-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5538-406">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-406">Option</span></span> | <span data-ttu-id="d5538-407">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-407">Default Value</span></span> | <span data-ttu-id="d5538-408">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5538-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-409">32 KB</span></span> | <span data-ttu-id="d5538-410">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5538-411">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5538-412">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5538-413">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5538-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-414">32 KB</span></span> | <span data-ttu-id="d5538-415">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5538-416">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5538-417">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="d5538-417">All Transports are enabled.</span></span> | <span data-ttu-id="d5538-418">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5538-419">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-419">See below.</span></span> | <span data-ttu-id="d5538-420">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="d5538-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5538-421">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-421">See below.</span></span> | <span data-ttu-id="d5538-422">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="d5538-423">0</span><span class="sxs-lookup"><span data-stu-id="d5538-423">0</span></span> | <span data-ttu-id="d5538-424">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="d5538-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="d5538-425">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="d5538-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="d5538-426">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5538-427">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-427">Option</span></span> | <span data-ttu-id="d5538-428">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-428">Default Value</span></span> | <span data-ttu-id="d5538-429">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5538-430">90 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-430">90 seconds</span></span> | <span data-ttu-id="d5538-431">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5538-432">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5538-433">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5538-434">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-434">Option</span></span> | <span data-ttu-id="d5538-435">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-435">Default Value</span></span> | <span data-ttu-id="d5538-436">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5538-437">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-437">5 seconds</span></span> | <span data-ttu-id="d5538-438">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="d5538-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5538-439">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d5538-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5538-440">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5538-441">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="d5538-441">Configure client options</span></span>

<span data-ttu-id="d5538-442">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5538-443">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5538-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5538-444">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="d5538-444">Configure logging</span></span>

<span data-ttu-id="d5538-445">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5538-446">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="d5538-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5538-447">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="d5538-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-448">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="d5538-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5538-449">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="d5538-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5538-450">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5538-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5538-451">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d5538-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5538-452">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="d5538-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5538-453">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="d5538-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5538-454">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="d5538-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5538-455">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="d5538-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5538-456">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="d5538-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5538-457">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="d5538-457">The following table lists the available log levels.</span></span> <span data-ttu-id="d5538-458">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="d5538-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5538-459">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="d5538-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5538-460">string</span><span class="sxs-lookup"><span data-stu-id="d5538-460">String</span></span>                      | <span data-ttu-id="d5538-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5538-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5538-462">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="d5538-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5538-463">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5538-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5538-464">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5538-465">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5538-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5538-466">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5538-467">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="d5538-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5538-468">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="d5538-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5538-469">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="d5538-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5538-470">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="d5538-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5538-471">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="d5538-471">Configure allowed transports</span></span>

<span data-ttu-id="d5538-472">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5538-473">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="d5538-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5538-474">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d5538-474">All transports are enabled by default.</span></span>

<span data-ttu-id="d5538-475">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="d5538-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5538-476">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5538-477">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="d5538-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5538-478">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5538-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5538-479">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5538-480">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="d5538-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5538-481">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="d5538-481">Configure bearer authentication</span></span>

<span data-ttu-id="d5538-482">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5538-483">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5538-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5538-484">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5538-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5538-485">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5538-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5538-486">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5538-487">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5538-488">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5538-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5538-489">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="d5538-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5538-490">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="d5538-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5538-491">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5538-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5538-492">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="d5538-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-493">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-494">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-494">Option</span></span> | <span data-ttu-id="d5538-495">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-495">Default value</span></span> | <span data-ttu-id="d5538-496">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5538-497">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-498">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-498">Timeout for server activity.</span></span> <span data-ttu-id="d5538-499">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-500">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-501">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5538-502">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-502">15 seconds</span></span> | <span data-ttu-id="d5538-503">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-504">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-505">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-506">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-507">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-507">15 seconds</span></span> | <span data-ttu-id="d5538-508">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-509">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-510">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5538-511">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="d5538-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5538-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-513">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-513">Option</span></span> | <span data-ttu-id="d5538-514">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-514">Default value</span></span> | <span data-ttu-id="d5538-515">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5538-516">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-517">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-517">Timeout for server activity.</span></span> <span data-ttu-id="d5538-518">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5538-519">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-520">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5538-521">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-522">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-523">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-524">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-525">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-525">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-526">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-526">Option</span></span> | <span data-ttu-id="d5538-527">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-527">Default value</span></span> | <span data-ttu-id="d5538-528">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5538-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5538-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5538-530">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-531">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-531">Timeout for server activity.</span></span> <span data-ttu-id="d5538-532">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-533">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-534">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5538-535">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-535">15 seconds</span></span> | <span data-ttu-id="d5538-536">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-537">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-538">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-539">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5538-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5538-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5538-541">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-542">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-543">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-544">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5538-545">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-545">Configure additional options</span></span>

<span data-ttu-id="d5538-546">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="d5538-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-547">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-548">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="d5538-548">.NET Option</span></span> |  <span data-ttu-id="d5538-549">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-549">Default value</span></span> | <span data-ttu-id="d5538-550">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5538-551">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5538-552">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-553">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-554">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5538-555">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-555">Empty</span></span> | <span data-ttu-id="d5538-556">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="d5538-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5538-557">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-557">Empty</span></span> | <span data-ttu-id="d5538-558">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5538-559">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-559">Empty</span></span> | <span data-ttu-id="d5538-560">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5538-561">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-561">5 seconds</span></span> | <span data-ttu-id="d5538-562">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-562">WebSockets only.</span></span> <span data-ttu-id="d5538-563">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="d5538-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5538-564">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="d5538-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5538-565">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-565">Empty</span></span> | <span data-ttu-id="d5538-566">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5538-567">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5538-568">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5538-569">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="d5538-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5538-570">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="d5538-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5538-571">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="d5538-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5538-572">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5538-573">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5538-574">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="d5538-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5538-575">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d5538-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5538-576">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d5538-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5538-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-578">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-578">JavaScript Option</span></span> | <span data-ttu-id="d5538-579">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-579">Default Value</span></span> | <span data-ttu-id="d5538-580">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5538-581">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5538-582">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="d5538-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5538-583">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-584">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-585">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-586">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-586">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-587">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="d5538-587">Java Option</span></span> | <span data-ttu-id="d5538-588">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-588">Default Value</span></span> | <span data-ttu-id="d5538-589">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5538-590">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5538-591">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-592">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-593">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5538-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5538-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5538-595">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-595">Empty</span></span> | <span data-ttu-id="d5538-596">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5538-597">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5538-598">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5538-599">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5538-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5538-600">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5538-601">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5538-602">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5538-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5538-603">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5538-604">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="d5538-605">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d5538-606">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d5538-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5538-607">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="d5538-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5538-608">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="d5538-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="d5538-609">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5538-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="d5538-610">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5538-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5538-611">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="d5538-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5538-612">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="d5538-613">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="d5538-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="d5538-614">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="d5538-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5538-615">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-615">MessagePack serialization options</span></span>

<span data-ttu-id="d5538-616">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5538-617">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-618">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5538-619">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="d5538-619">Configure server options</span></span>

<span data-ttu-id="d5538-620">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="d5538-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5538-621">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-621">Option</span></span> | <span data-ttu-id="d5538-622">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-622">Default Value</span></span> | <span data-ttu-id="d5538-623">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5538-624">30 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-624">30 seconds</span></span> | <span data-ttu-id="d5538-625">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5538-626">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5538-627">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5538-628">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-628">15 seconds</span></span> | <span data-ttu-id="d5538-629">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="d5538-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5538-630">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-631">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-632">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-632">15 seconds</span></span> | <span data-ttu-id="d5538-633">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5538-634">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="d5538-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5538-635">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5538-636">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="d5538-636">All installed protocols</span></span> | <span data-ttu-id="d5538-637">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-637">Protocols supported by this hub.</span></span> <span data-ttu-id="d5538-638">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5538-639">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5538-640">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="d5538-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="d5538-641">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="d5538-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="d5538-642">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="d5538-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="d5538-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-643">32 KB</span></span> | <span data-ttu-id="d5538-644">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="d5538-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="d5538-645">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5538-646">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5538-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5538-647">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="d5538-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5538-648">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5538-649">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5538-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5538-650">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5538-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5538-651">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-651">Option</span></span> | <span data-ttu-id="d5538-652">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-652">Default Value</span></span> | <span data-ttu-id="d5538-653">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5538-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-654">32 KB</span></span> | <span data-ttu-id="d5538-655">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="d5538-656">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5538-657">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5538-658">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5538-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-659">32 KB</span></span> | <span data-ttu-id="d5538-660">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="d5538-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="d5538-661">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5538-662">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="d5538-662">All Transports are enabled.</span></span> | <span data-ttu-id="d5538-663">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5538-664">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-664">See below.</span></span> | <span data-ttu-id="d5538-665">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="d5538-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5538-666">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-666">See below.</span></span> | <span data-ttu-id="d5538-667">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5538-668">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5538-669">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-669">Option</span></span> | <span data-ttu-id="d5538-670">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-670">Default Value</span></span> | <span data-ttu-id="d5538-671">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5538-672">90 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-672">90 seconds</span></span> | <span data-ttu-id="d5538-673">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5538-674">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5538-675">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5538-676">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-676">Option</span></span> | <span data-ttu-id="d5538-677">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-677">Default Value</span></span> | <span data-ttu-id="d5538-678">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5538-679">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-679">5 seconds</span></span> | <span data-ttu-id="d5538-680">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="d5538-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5538-681">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d5538-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5538-682">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5538-683">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="d5538-683">Configure client options</span></span>

<span data-ttu-id="d5538-684">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5538-685">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5538-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5538-686">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="d5538-686">Configure logging</span></span>

<span data-ttu-id="d5538-687">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5538-688">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="d5538-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5538-689">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="d5538-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-690">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="d5538-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5538-691">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="d5538-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5538-692">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5538-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5538-693">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d5538-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5538-694">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="d5538-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5538-695">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="d5538-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5538-696">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="d5538-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="d5538-697">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="d5538-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="d5538-698">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="d5538-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="d5538-699">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="d5538-699">The following table lists the available log levels.</span></span> <span data-ttu-id="d5538-700">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="d5538-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="d5538-701">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="d5538-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="d5538-702">string</span><span class="sxs-lookup"><span data-stu-id="d5538-702">String</span></span>                      | <span data-ttu-id="d5538-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="d5538-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="d5538-704">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="d5538-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="d5538-705">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="d5538-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="d5538-706">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5538-707">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5538-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5538-708">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5538-709">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="d5538-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5538-710">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="d5538-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5538-711">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="d5538-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5538-712">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="d5538-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5538-713">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="d5538-713">Configure allowed transports</span></span>

<span data-ttu-id="d5538-714">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5538-715">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="d5538-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5538-716">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d5538-716">All transports are enabled by default.</span></span>

<span data-ttu-id="d5538-717">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="d5538-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5538-718">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5538-719">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="d5538-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="d5538-720">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="d5538-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="d5538-721">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5538-722">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="d5538-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5538-723">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="d5538-723">Configure bearer authentication</span></span>

<span data-ttu-id="d5538-724">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5538-725">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5538-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5538-726">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5538-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5538-727">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5538-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5538-728">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5538-729">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5538-730">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5538-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5538-731">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="d5538-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5538-732">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="d5538-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5538-733">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5538-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5538-734">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="d5538-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-735">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-736">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-736">Option</span></span> | <span data-ttu-id="d5538-737">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-737">Default value</span></span> | <span data-ttu-id="d5538-738">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5538-739">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-740">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-740">Timeout for server activity.</span></span> <span data-ttu-id="d5538-741">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-742">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-743">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5538-744">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-744">15 seconds</span></span> | <span data-ttu-id="d5538-745">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-746">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-747">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-748">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-749">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-749">15 seconds</span></span> | <span data-ttu-id="d5538-750">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-751">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-752">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5538-753">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="d5538-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5538-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-755">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-755">Option</span></span> | <span data-ttu-id="d5538-756">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-756">Default value</span></span> | <span data-ttu-id="d5538-757">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5538-758">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-759">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-759">Timeout for server activity.</span></span> <span data-ttu-id="d5538-760">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5538-761">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-762">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5538-763">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-764">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-765">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-766">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-767">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-767">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-768">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-768">Option</span></span> | <span data-ttu-id="d5538-769">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-769">Default value</span></span> | <span data-ttu-id="d5538-770">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5538-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5538-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5538-772">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-773">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-773">Timeout for server activity.</span></span> <span data-ttu-id="d5538-774">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-775">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-776">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5538-777">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-777">15 seconds</span></span> | <span data-ttu-id="d5538-778">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-779">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-780">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-781">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5538-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5538-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5538-783">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-784">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-785">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-786">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5538-787">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-787">Configure additional options</span></span>

<span data-ttu-id="d5538-788">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="d5538-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-789">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-790">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="d5538-790">.NET Option</span></span> |  <span data-ttu-id="d5538-791">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-791">Default value</span></span> | <span data-ttu-id="d5538-792">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5538-793">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5538-794">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-795">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-796">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5538-797">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-797">Empty</span></span> | <span data-ttu-id="d5538-798">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="d5538-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5538-799">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-799">Empty</span></span> | <span data-ttu-id="d5538-800">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5538-801">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-801">Empty</span></span> | <span data-ttu-id="d5538-802">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5538-803">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-803">5 seconds</span></span> | <span data-ttu-id="d5538-804">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-804">WebSockets only.</span></span> <span data-ttu-id="d5538-805">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="d5538-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5538-806">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="d5538-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5538-807">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-807">Empty</span></span> | <span data-ttu-id="d5538-808">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5538-809">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5538-810">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5538-811">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="d5538-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5538-812">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="d5538-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5538-813">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="d5538-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5538-814">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5538-815">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5538-816">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="d5538-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5538-817">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d5538-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5538-818">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d5538-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5538-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-820">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-820">JavaScript Option</span></span> | <span data-ttu-id="d5538-821">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-821">Default Value</span></span> | <span data-ttu-id="d5538-822">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5538-823">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5538-824">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="d5538-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5538-825">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-826">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-827">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-828">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-828">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-829">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="d5538-829">Java Option</span></span> | <span data-ttu-id="d5538-830">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-830">Default Value</span></span> | <span data-ttu-id="d5538-831">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5538-832">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5538-833">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-834">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-835">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5538-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5538-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5538-837">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-837">Empty</span></span> | <span data-ttu-id="d5538-838">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5538-839">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5538-840">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5538-841">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5538-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5538-842">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5538-843">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5538-844">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5538-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5538-845">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5538-846">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5538-847">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d5538-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5538-848">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="d5538-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5538-849">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5538-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5538-850">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5538-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5538-851">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5538-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5538-852">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="d5538-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5538-853">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5538-854">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-854">MessagePack serialization options</span></span>

<span data-ttu-id="d5538-855">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5538-856">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-857">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5538-858">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="d5538-858">Configure server options</span></span>

<span data-ttu-id="d5538-859">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="d5538-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5538-860">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-860">Option</span></span> | <span data-ttu-id="d5538-861">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-861">Default Value</span></span> | <span data-ttu-id="d5538-862">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="d5538-863">30 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-863">30 seconds</span></span> | <span data-ttu-id="d5538-864">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="d5538-865">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="d5538-866">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="d5538-867">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-867">15 seconds</span></span> | <span data-ttu-id="d5538-868">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="d5538-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5538-869">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-870">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-871">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-871">15 seconds</span></span> | <span data-ttu-id="d5538-872">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5538-873">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="d5538-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5538-874">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5538-875">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="d5538-875">All installed protocols</span></span> | <span data-ttu-id="d5538-876">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-876">Protocols supported by this hub.</span></span> <span data-ttu-id="d5538-877">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5538-878">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5538-879">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="d5538-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5538-880">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5538-881">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5538-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5538-882">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="d5538-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5538-883">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5538-884">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5538-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5538-885">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5538-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5538-886">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-886">Option</span></span> | <span data-ttu-id="d5538-887">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-887">Default Value</span></span> | <span data-ttu-id="d5538-888">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5538-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-889">32 KB</span></span> | <span data-ttu-id="d5538-890">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="d5538-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5538-891">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5538-892">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5538-893">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5538-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-894">32 KB</span></span> | <span data-ttu-id="d5538-895">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="d5538-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5538-896">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5538-897">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="d5538-897">All Transports are enabled.</span></span> | <span data-ttu-id="d5538-898">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5538-899">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-899">See below.</span></span> | <span data-ttu-id="d5538-900">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="d5538-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5538-901">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-901">See below.</span></span> | <span data-ttu-id="d5538-902">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5538-903">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5538-904">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-904">Option</span></span> | <span data-ttu-id="d5538-905">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-905">Default Value</span></span> | <span data-ttu-id="d5538-906">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5538-907">90 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-907">90 seconds</span></span> | <span data-ttu-id="d5538-908">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5538-909">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5538-910">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5538-911">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-911">Option</span></span> | <span data-ttu-id="d5538-912">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-912">Default Value</span></span> | <span data-ttu-id="d5538-913">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5538-914">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-914">5 seconds</span></span> | <span data-ttu-id="d5538-915">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="d5538-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5538-916">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d5538-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5538-917">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5538-918">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="d5538-918">Configure client options</span></span>

<span data-ttu-id="d5538-919">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5538-920">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5538-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5538-921">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="d5538-921">Configure logging</span></span>

<span data-ttu-id="d5538-922">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5538-923">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="d5538-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5538-924">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="d5538-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-925">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="d5538-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5538-926">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="d5538-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5538-927">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5538-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5538-928">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d5538-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5538-929">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="d5538-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5538-930">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="d5538-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5538-931">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="d5538-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5538-932">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5538-933">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5538-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5538-934">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5538-935">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="d5538-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5538-936">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="d5538-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5538-937">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="d5538-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5538-938">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="d5538-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5538-939">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="d5538-939">Configure allowed transports</span></span>

<span data-ttu-id="d5538-940">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5538-941">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="d5538-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5538-942">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d5538-942">All transports are enabled by default.</span></span>

<span data-ttu-id="d5538-943">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="d5538-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5538-944">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="d5538-945">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="d5538-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5538-946">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="d5538-946">Configure bearer authentication</span></span>

<span data-ttu-id="d5538-947">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5538-948">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5538-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5538-949">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5538-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5538-950">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5538-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5538-951">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5538-952">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5538-953">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5538-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5538-954">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="d5538-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5538-955">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="d5538-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5538-956">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5538-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5538-957">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="d5538-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-958">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-959">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-959">Option</span></span> | <span data-ttu-id="d5538-960">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-960">Default value</span></span> | <span data-ttu-id="d5538-961">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5538-962">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-963">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-963">Timeout for server activity.</span></span> <span data-ttu-id="d5538-964">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-965">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-966">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5538-967">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-967">15 seconds</span></span> | <span data-ttu-id="d5538-968">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-969">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-970">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-971">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-972">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-972">15 seconds</span></span> | <span data-ttu-id="d5538-973">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-974">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-975">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="d5538-976">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="d5538-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5538-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-978">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-978">Option</span></span> | <span data-ttu-id="d5538-979">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-979">Default value</span></span> | <span data-ttu-id="d5538-980">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5538-981">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-982">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-982">Timeout for server activity.</span></span> <span data-ttu-id="d5538-983">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5538-984">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-985">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="d5538-986">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-987">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-988">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-989">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-990">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-990">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-991">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-991">Option</span></span> | <span data-ttu-id="d5538-992">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-992">Default value</span></span> | <span data-ttu-id="d5538-993">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5538-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5538-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5538-995">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-996">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-996">Timeout for server activity.</span></span> <span data-ttu-id="d5538-997">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-998">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-999">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5538-1000">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1000">15 seconds</span></span> | <span data-ttu-id="d5538-1001">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-1002">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-1003">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-1004">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="d5538-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="d5538-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="d5538-1006">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="d5538-1007">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="d5538-1008">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="d5538-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="d5538-1009">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="d5538-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5538-1010">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-1010">Configure additional options</span></span>

<span data-ttu-id="d5538-1011">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="d5538-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-1013">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="d5538-1013">.NET Option</span></span> |  <span data-ttu-id="d5538-1014">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1014">Default value</span></span> | <span data-ttu-id="d5538-1015">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5538-1016">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5538-1017">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1018">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1019">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5538-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1020">Empty</span></span> | <span data-ttu-id="d5538-1021">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="d5538-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5538-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1022">Empty</span></span> | <span data-ttu-id="d5538-1023">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5538-1024">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1024">Empty</span></span> | <span data-ttu-id="d5538-1025">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5538-1026">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1026">5 seconds</span></span> | <span data-ttu-id="d5538-1027">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1027">WebSockets only.</span></span> <span data-ttu-id="d5538-1028">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="d5538-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5538-1029">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="d5538-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5538-1030">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1030">Empty</span></span> | <span data-ttu-id="d5538-1031">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5538-1032">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5538-1033">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5538-1034">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="d5538-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5538-1035">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="d5538-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5538-1036">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="d5538-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5538-1037">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5538-1038">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5538-1039">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="d5538-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5538-1040">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d5538-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5538-1041">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d5538-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5538-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-1043">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-1043">JavaScript Option</span></span> | <span data-ttu-id="d5538-1044">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1044">Default Value</span></span> | <span data-ttu-id="d5538-1045">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5538-1046">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5538-1047">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="d5538-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5538-1048">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1049">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1050">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-1051">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-1052">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="d5538-1052">Java Option</span></span> | <span data-ttu-id="d5538-1053">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1053">Default Value</span></span> | <span data-ttu-id="d5538-1054">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5538-1055">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5538-1056">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1057">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1058">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5538-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5538-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5538-1060">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1060">Empty</span></span> | <span data-ttu-id="d5538-1061">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5538-1062">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5538-1063">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5538-1064">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5538-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5538-1065">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="d5538-1066">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="d5538-1067">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="d5538-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="d5538-1068">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="d5538-1069">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d5538-1070">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="d5538-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="d5538-1071">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="d5538-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="d5538-1072">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="d5538-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="d5538-1073">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="d5538-1074">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="d5538-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="d5538-1075">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="d5538-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="d5538-1076">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="d5538-1077">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="d5538-1077">MessagePack serialization options</span></span>

<span data-ttu-id="d5538-1078">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="d5538-1079">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="d5538-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-1080">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="d5538-1081">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="d5538-1081">Configure server options</span></span>

<span data-ttu-id="d5538-1082">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="d5538-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="d5538-1083">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1083">Option</span></span> | <span data-ttu-id="d5538-1084">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1084">Default Value</span></span> | <span data-ttu-id="d5538-1085">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="d5538-1086">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1086">15 seconds</span></span> | <span data-ttu-id="d5538-1087">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="d5538-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="d5538-1088">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-1089">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="d5538-1090">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1090">15 seconds</span></span> | <span data-ttu-id="d5538-1091">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="d5538-1092">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="d5538-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="d5538-1093">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="d5538-1094">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="d5538-1094">All installed protocols</span></span> | <span data-ttu-id="d5538-1095">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="d5538-1096">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="d5538-1097">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="d5538-1098">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="d5538-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="d5538-1099">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="d5538-1100">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="d5538-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="d5538-1101">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="d5538-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="d5538-1102">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="d5538-1103">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="d5538-1104">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d5538-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="d5538-1105">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1105">Option</span></span> | <span data-ttu-id="d5538-1106">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1106">Default Value</span></span> | <span data-ttu-id="d5538-1107">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="d5538-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-1108">32 KB</span></span> | <span data-ttu-id="d5538-1109">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="d5538-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="d5538-1110">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="d5538-1111">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="d5538-1112">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="d5538-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="d5538-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="d5538-1113">32 KB</span></span> | <span data-ttu-id="d5538-1114">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="d5538-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="d5538-1115">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="d5538-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="d5538-1116">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="d5538-1116">All Transports are enabled.</span></span> | <span data-ttu-id="d5538-1117">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="d5538-1118">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-1118">See below.</span></span> | <span data-ttu-id="d5538-1119">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="d5538-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="d5538-1120">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="d5538-1120">See below.</span></span> | <span data-ttu-id="d5538-1121">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="d5538-1122">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="d5538-1123">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1123">Option</span></span> | <span data-ttu-id="d5538-1124">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1124">Default Value</span></span> | <span data-ttu-id="d5538-1125">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="d5538-1126">90 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1126">90 seconds</span></span> | <span data-ttu-id="d5538-1127">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="d5538-1128">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="d5538-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="d5538-1129">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="d5538-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="d5538-1130">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1130">Option</span></span> | <span data-ttu-id="d5538-1131">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1131">Default Value</span></span> | <span data-ttu-id="d5538-1132">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="d5538-1133">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1133">5 seconds</span></span> | <span data-ttu-id="d5538-1134">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="d5538-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="d5538-1135">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="d5538-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="d5538-1136">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="d5538-1137">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="d5538-1137">Configure client options</span></span>

<span data-ttu-id="d5538-1138">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d5538-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="d5538-1139">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="d5538-1140">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="d5538-1140">Configure logging</span></span>

<span data-ttu-id="d5538-1141">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="d5538-1142">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="d5538-1143">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="d5538-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="d5538-1144">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="d5538-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="d5538-1145">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="d5538-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="d5538-1146">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="d5538-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="d5538-1147">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="d5538-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="d5538-1148">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="d5538-1149">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="d5538-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="d5538-1150">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="d5538-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="d5538-1151">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="d5538-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="d5538-1152">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="d5538-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="d5538-1153">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d5538-1154">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="d5538-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d5538-1155">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="d5538-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d5538-1156">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="d5538-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d5538-1157">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="d5538-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="d5538-1158">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="d5538-1158">Configure allowed transports</span></span>

<span data-ttu-id="d5538-1159">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="d5538-1160">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="d5538-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="d5538-1161">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="d5538-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="d5538-1162">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="d5538-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="d5538-1163">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="d5538-1164">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="d5538-1164">Configure bearer authentication</span></span>

<span data-ttu-id="d5538-1165">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="d5538-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="d5538-1166">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="d5538-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="d5538-1167">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="d5538-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="d5538-1168">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="d5538-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="d5538-1169">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="d5538-1170">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="d5538-1171">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="d5538-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d5538-1172">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="d5538-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d5538-1173">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="d5538-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="d5538-1174">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="d5538-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="d5538-1175">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="d5538-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-1177">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1177">Option</span></span> | <span data-ttu-id="d5538-1178">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1178">Default value</span></span> | <span data-ttu-id="d5538-1179">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="d5538-1180">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-1181">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1181">Timeout for server activity.</span></span> <span data-ttu-id="d5538-1182">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-1183">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-1184">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="d5538-1185">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1185">15 seconds</span></span> | <span data-ttu-id="d5538-1186">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-1187">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="d5538-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="d5538-1188">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-1189">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="d5538-1190">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="d5538-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="d5538-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-1192">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1192">Option</span></span> | <span data-ttu-id="d5538-1193">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1193">Default value</span></span> | <span data-ttu-id="d5538-1194">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="d5538-1195">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-1196">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1196">Timeout for server activity.</span></span> <span data-ttu-id="d5538-1197">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="d5538-1198">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-1199">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-1200">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-1201">Opzione</span><span class="sxs-lookup"><span data-stu-id="d5538-1201">Option</span></span> | <span data-ttu-id="d5538-1202">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1202">Default value</span></span> | <span data-ttu-id="d5538-1203">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="d5538-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="d5538-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="d5538-1205">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="d5538-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="d5538-1206">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1206">Timeout for server activity.</span></span> <span data-ttu-id="d5538-1207">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-1208">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="d5538-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="d5538-1209">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` , per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="d5538-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="d5538-1210">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1210">15 seconds</span></span> | <span data-ttu-id="d5538-1211">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="d5538-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="d5538-1212">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="d5538-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="d5538-1213">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="d5538-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="d5538-1214">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="d5538-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="d5538-1215">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-1215">Configure additional options</span></span>

<span data-ttu-id="d5538-1216">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="d5538-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="d5538-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="d5538-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="d5538-1218">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="d5538-1218">.NET Option</span></span> |  <span data-ttu-id="d5538-1219">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1219">Default value</span></span> | <span data-ttu-id="d5538-1220">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="d5538-1221">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="d5538-1222">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1223">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1224">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="d5538-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1225">Empty</span></span> | <span data-ttu-id="d5538-1226">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="d5538-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="d5538-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1227">Empty</span></span> | <span data-ttu-id="d5538-1228">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="d5538-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1229">Empty</span></span> | <span data-ttu-id="d5538-1230">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="d5538-1231">5 secondi</span><span class="sxs-lookup"><span data-stu-id="d5538-1231">5 seconds</span></span> | <span data-ttu-id="d5538-1232">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1232">WebSockets only.</span></span> <span data-ttu-id="d5538-1233">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="d5538-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="d5538-1234">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="d5538-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="d5538-1235">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1235">Empty</span></span> | <span data-ttu-id="d5538-1236">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="d5538-1237">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="d5538-1238">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="d5538-1239">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="d5538-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="d5538-1240">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="d5538-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="d5538-1241">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="d5538-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="d5538-1242">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="d5538-1243">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="d5538-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="d5538-1244">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="d5538-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="d5538-1245">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="d5538-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="d5538-1246">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="d5538-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="d5538-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="d5538-1248">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="d5538-1248">JavaScript Option</span></span> | <span data-ttu-id="d5538-1249">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1249">Default Value</span></span> | <span data-ttu-id="d5538-1250">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="d5538-1251">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="d5538-1252">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="d5538-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="d5538-1253">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1254">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1255">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="d5538-1256">Java</span><span class="sxs-lookup"><span data-stu-id="d5538-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="d5538-1257">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="d5538-1257">Java Option</span></span> | <span data-ttu-id="d5538-1258">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="d5538-1258">Default Value</span></span> | <span data-ttu-id="d5538-1259">Descrizione</span><span class="sxs-lookup"><span data-stu-id="d5538-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="d5538-1260">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="d5538-1261">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="d5538-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="d5538-1262">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="d5538-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="d5538-1263">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="d5538-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="d5538-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="d5538-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="d5538-1265">Empty</span><span class="sxs-lookup"><span data-stu-id="d5538-1265">Empty</span></span> | <span data-ttu-id="d5538-1266">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="d5538-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="d5538-1267">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="d5538-1268">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="d5538-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="d5538-1269">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="d5538-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="d5538-1270">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="d5538-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
