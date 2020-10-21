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
ms.openlocfilehash: 8851246dbaa076af1fdbc4e5e4f1ada0e4e3988a
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326585"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="b90fc-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="b90fc-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b90fc-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b90fc-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b90fc-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b90fc-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b90fc-108">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b90fc-109">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b90fc-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b90fc-111">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b90fc-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b90fc-112">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b90fc-113">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b90fc-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b90fc-114">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="b90fc-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b90fc-115">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b90fc-116">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b90fc-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b90fc-117">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b90fc-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b90fc-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-118">MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b90fc-120">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-121">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b90fc-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="b90fc-122">Configure server options</span></span>

<span data-ttu-id="b90fc-123">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="b90fc-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b90fc-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-124">Option</span></span> | <span data-ttu-id="b90fc-125">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-125">Default Value</span></span> | <span data-ttu-id="b90fc-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b90fc-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-127">30 seconds</span></span> | <span data-ttu-id="b90fc-128">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b90fc-129">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b90fc-130">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b90fc-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-131">15 seconds</span></span> | <span data-ttu-id="b90fc-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="b90fc-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b90fc-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-134">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-135">15 seconds</span></span> | <span data-ttu-id="b90fc-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b90fc-137">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b90fc-138">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b90fc-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="b90fc-139">All installed protocols</span></span> | <span data-ttu-id="b90fc-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-140">Protocols supported by this hub.</span></span> <span data-ttu-id="b90fc-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b90fc-142">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b90fc-143">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b90fc-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b90fc-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b90fc-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-146">32 KB</span></span> | <span data-ttu-id="b90fc-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="b90fc-148">1</span><span class="sxs-lookup"><span data-stu-id="b90fc-148">1</span></span> | <span data-ttu-id="b90fc-149">Numero massimo di metodi dell'hub che ogni client può chiamare in parallelo prima della coda.</span><span class="sxs-lookup"><span data-stu-id="b90fc-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="b90fc-150">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b90fc-151">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b90fc-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b90fc-152">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="b90fc-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b90fc-153">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b90fc-154">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b90fc-155">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b90fc-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b90fc-156">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-156">Option</span></span> | <span data-ttu-id="b90fc-157">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-157">Default Value</span></span> | <span data-ttu-id="b90fc-158">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b90fc-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-159">32 KB</span></span> | <span data-ttu-id="b90fc-160">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b90fc-161">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b90fc-162">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b90fc-163">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b90fc-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-164">32 KB</span></span> | <span data-ttu-id="b90fc-165">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b90fc-166">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b90fc-167">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-167">All Transports are enabled.</span></span> | <span data-ttu-id="b90fc-168">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b90fc-169">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-169">See below.</span></span> | <span data-ttu-id="b90fc-170">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b90fc-171">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-171">See below.</span></span> | <span data-ttu-id="b90fc-172">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b90fc-173">0</span><span class="sxs-lookup"><span data-stu-id="b90fc-173">0</span></span> | <span data-ttu-id="b90fc-174">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b90fc-175">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b90fc-176">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b90fc-177">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-177">Option</span></span> | <span data-ttu-id="b90fc-178">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-178">Default Value</span></span> | <span data-ttu-id="b90fc-179">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b90fc-180">90 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-180">90 seconds</span></span> | <span data-ttu-id="b90fc-181">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b90fc-182">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b90fc-183">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b90fc-184">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-184">Option</span></span> | <span data-ttu-id="b90fc-185">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-185">Default Value</span></span> | <span data-ttu-id="b90fc-186">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b90fc-187">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-187">5 seconds</span></span> | <span data-ttu-id="b90fc-188">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="b90fc-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b90fc-189">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b90fc-190">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b90fc-191">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="b90fc-191">Configure client options</span></span>

<span data-ttu-id="b90fc-192">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b90fc-193">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b90fc-194">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="b90fc-194">Configure logging</span></span>

<span data-ttu-id="b90fc-195">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b90fc-196">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b90fc-197">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-198">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="b90fc-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b90fc-199">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="b90fc-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b90fc-200">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90fc-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b90fc-201">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b90fc-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b90fc-202">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b90fc-203">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="b90fc-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b90fc-204">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="b90fc-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b90fc-205">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="b90fc-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b90fc-206">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b90fc-207">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="b90fc-207">The following table lists the available log levels.</span></span> <span data-ttu-id="b90fc-208">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b90fc-209">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b90fc-210">string</span><span class="sxs-lookup"><span data-stu-id="b90fc-210">String</span></span>                      | <span data-ttu-id="b90fc-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b90fc-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b90fc-212">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="b90fc-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b90fc-213">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="b90fc-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b90fc-214">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b90fc-215">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b90fc-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b90fc-216">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b90fc-217">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="b90fc-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b90fc-218">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="b90fc-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b90fc-219">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b90fc-220">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b90fc-221">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="b90fc-221">Configure allowed transports</span></span>

<span data-ttu-id="b90fc-222">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b90fc-223">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b90fc-224">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b90fc-224">All transports are enabled by default.</span></span>

<span data-ttu-id="b90fc-225">Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="b90fc-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b90fc-226">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b90fc-227">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="b90fc-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b90fc-228">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b90fc-229">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b90fc-230">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b90fc-231">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="b90fc-231">Configure bearer authentication</span></span>

<span data-ttu-id="b90fc-232">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b90fc-233">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b90fc-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b90fc-234">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b90fc-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b90fc-235">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b90fc-236">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b90fc-237">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b90fc-238">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b90fc-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b90fc-239">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b90fc-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b90fc-240">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b90fc-241">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b90fc-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b90fc-242">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-243">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-244">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-244">Option</span></span> | <span data-ttu-id="b90fc-245">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-245">Default value</span></span> | <span data-ttu-id="b90fc-246">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b90fc-247">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-248">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-248">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-249">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-250">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-251">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-252">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-252">15 seconds</span></span> | <span data-ttu-id="b90fc-253">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-254">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-255">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-256">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-257">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-257">15 seconds</span></span> | <span data-ttu-id="b90fc-258">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-259">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-260">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b90fc-261">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="b90fc-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b90fc-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-263">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-263">Option</span></span> | <span data-ttu-id="b90fc-264">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-264">Default value</span></span> | <span data-ttu-id="b90fc-265">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b90fc-266">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-267">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-267">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-268">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b90fc-269">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-270">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b90fc-271">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-272">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-273">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-274">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-275">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-275">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-276">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-276">Option</span></span> | <span data-ttu-id="b90fc-277">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-277">Default value</span></span> | <span data-ttu-id="b90fc-278">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b90fc-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b90fc-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b90fc-280">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-281">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-281">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-282">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-283">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-284">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b90fc-285">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-285">15 seconds</span></span> | <span data-ttu-id="b90fc-286">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-287">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-288">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-289">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b90fc-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b90fc-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b90fc-291">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-292">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-293">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-294">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b90fc-295">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-295">Configure additional options</span></span>

<span data-ttu-id="b90fc-296">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="b90fc-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-297">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-298">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-298">.NET Option</span></span> |  <span data-ttu-id="b90fc-299">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-299">Default value</span></span> | <span data-ttu-id="b90fc-300">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b90fc-301">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b90fc-302">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-303">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-304">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b90fc-305">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-305">Empty</span></span> | <span data-ttu-id="b90fc-306">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b90fc-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b90fc-307">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-307">Empty</span></span> | <span data-ttu-id="b90fc-308">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b90fc-309">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-309">Empty</span></span> | <span data-ttu-id="b90fc-310">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b90fc-311">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-311">5 seconds</span></span> | <span data-ttu-id="b90fc-312">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-312">WebSockets only.</span></span> <span data-ttu-id="b90fc-313">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b90fc-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b90fc-314">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="b90fc-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b90fc-315">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-315">Empty</span></span> | <span data-ttu-id="b90fc-316">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b90fc-317">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b90fc-318">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="b90fc-319">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b90fc-320">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="b90fc-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b90fc-321">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="b90fc-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b90fc-322">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b90fc-323">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b90fc-324">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="b90fc-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b90fc-325">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b90fc-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b90fc-326">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="b90fc-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b90fc-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-328">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-328">JavaScript Option</span></span> | <span data-ttu-id="b90fc-329">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-329">Default Value</span></span> | <span data-ttu-id="b90fc-330">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b90fc-331">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b90fc-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="b90fc-333">Dizionario di intestazioni inviate con tutte le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="b90fc-334">L'invio di intestazioni nel browser non funziona per WebSocket o per il <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flusso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b90fc-335">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b90fc-336">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-337">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-337">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-338">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-338">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="b90fc-339">Specifica se le credenziali verranno inviate con la richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="b90fc-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="b90fc-340">App Azure servizio usa cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per il corretto funzionamento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-340">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="b90fc-341">Per ulteriori informazioni su CORS con SignalR , vedere <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="b90fc-341">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-342">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-342">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-343">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-343">Java Option</span></span> | <span data-ttu-id="b90fc-344">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-344">Default Value</span></span> | <span data-ttu-id="b90fc-345">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b90fc-346">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b90fc-347">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-348">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-348">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-349">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-349">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b90fc-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b90fc-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b90fc-351">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-351">Empty</span></span> | <span data-ttu-id="b90fc-352">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b90fc-353">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

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

<span data-ttu-id="b90fc-354">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="b90fc-355">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b90fc-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b90fc-356">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b90fc-357">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-358">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b90fc-358">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b90fc-359">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b90fc-360">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b90fc-361">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-361">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b90fc-362">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b90fc-363">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b90fc-364">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b90fc-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b90fc-365">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b90fc-366">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b90fc-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b90fc-367">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="b90fc-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b90fc-368">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b90fc-369">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b90fc-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b90fc-370">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b90fc-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b90fc-371">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-371">MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-372">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b90fc-373">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-373">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-374">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b90fc-375">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="b90fc-375">Configure server options</span></span>

<span data-ttu-id="b90fc-376">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="b90fc-376">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b90fc-377">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-377">Option</span></span> | <span data-ttu-id="b90fc-378">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-378">Default Value</span></span> | <span data-ttu-id="b90fc-379">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b90fc-380">30 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-380">30 seconds</span></span> | <span data-ttu-id="b90fc-381">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b90fc-382">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b90fc-383">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b90fc-384">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-384">15 seconds</span></span> | <span data-ttu-id="b90fc-385">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="b90fc-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b90fc-386">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-387">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-387">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-388">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-388">15 seconds</span></span> | <span data-ttu-id="b90fc-389">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b90fc-390">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b90fc-391">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b90fc-392">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="b90fc-392">All installed protocols</span></span> | <span data-ttu-id="b90fc-393">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-393">Protocols supported by this hub.</span></span> <span data-ttu-id="b90fc-394">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b90fc-395">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b90fc-396">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b90fc-397">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b90fc-398">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b90fc-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-399">32 KB</span></span> | <span data-ttu-id="b90fc-400">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b90fc-401">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-401">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b90fc-402">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b90fc-402">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b90fc-403">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="b90fc-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b90fc-404">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b90fc-405">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b90fc-406">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b90fc-406">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b90fc-407">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-407">Option</span></span> | <span data-ttu-id="b90fc-408">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-408">Default Value</span></span> | <span data-ttu-id="b90fc-409">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b90fc-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-410">32 KB</span></span> | <span data-ttu-id="b90fc-411">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b90fc-412">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b90fc-413">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b90fc-414">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b90fc-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-415">32 KB</span></span> | <span data-ttu-id="b90fc-416">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b90fc-417">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b90fc-418">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-418">All Transports are enabled.</span></span> | <span data-ttu-id="b90fc-419">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b90fc-420">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-420">See below.</span></span> | <span data-ttu-id="b90fc-421">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b90fc-422">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-422">See below.</span></span> | <span data-ttu-id="b90fc-423">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b90fc-424">0</span><span class="sxs-lookup"><span data-stu-id="b90fc-424">0</span></span> | <span data-ttu-id="b90fc-425">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b90fc-426">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b90fc-427">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b90fc-428">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-428">Option</span></span> | <span data-ttu-id="b90fc-429">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-429">Default Value</span></span> | <span data-ttu-id="b90fc-430">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b90fc-431">90 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-431">90 seconds</span></span> | <span data-ttu-id="b90fc-432">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b90fc-433">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b90fc-434">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b90fc-435">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-435">Option</span></span> | <span data-ttu-id="b90fc-436">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-436">Default Value</span></span> | <span data-ttu-id="b90fc-437">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b90fc-438">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-438">5 seconds</span></span> | <span data-ttu-id="b90fc-439">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="b90fc-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b90fc-440">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b90fc-441">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b90fc-442">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="b90fc-442">Configure client options</span></span>

<span data-ttu-id="b90fc-443">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b90fc-444">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b90fc-445">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="b90fc-445">Configure logging</span></span>

<span data-ttu-id="b90fc-446">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b90fc-447">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b90fc-448">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-449">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="b90fc-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b90fc-450">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="b90fc-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b90fc-451">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90fc-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b90fc-452">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b90fc-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b90fc-453">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b90fc-454">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="b90fc-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b90fc-455">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="b90fc-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b90fc-456">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="b90fc-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b90fc-457">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-457">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b90fc-458">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="b90fc-458">The following table lists the available log levels.</span></span> <span data-ttu-id="b90fc-459">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b90fc-460">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-460">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b90fc-461">string</span><span class="sxs-lookup"><span data-stu-id="b90fc-461">String</span></span>                      | <span data-ttu-id="b90fc-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b90fc-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b90fc-463">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="b90fc-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b90fc-464">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="b90fc-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b90fc-465">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b90fc-466">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b90fc-466">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b90fc-467">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-467">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b90fc-468">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="b90fc-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b90fc-469">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="b90fc-469">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b90fc-470">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b90fc-471">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b90fc-472">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="b90fc-472">Configure allowed transports</span></span>

<span data-ttu-id="b90fc-473">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-473">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b90fc-474">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b90fc-475">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b90fc-475">All transports are enabled by default.</span></span>

<span data-ttu-id="b90fc-476">Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="b90fc-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b90fc-477">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b90fc-478">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="b90fc-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b90fc-479">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b90fc-480">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b90fc-481">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-481">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b90fc-482">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="b90fc-482">Configure bearer authentication</span></span>

<span data-ttu-id="b90fc-483">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-483">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b90fc-484">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b90fc-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b90fc-485">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b90fc-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b90fc-486">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b90fc-487">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b90fc-488">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b90fc-489">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b90fc-489">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b90fc-490">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b90fc-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b90fc-491">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b90fc-492">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b90fc-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b90fc-493">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-494">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-495">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-495">Option</span></span> | <span data-ttu-id="b90fc-496">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-496">Default value</span></span> | <span data-ttu-id="b90fc-497">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b90fc-498">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-499">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-499">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-500">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-501">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-502">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-503">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-503">15 seconds</span></span> | <span data-ttu-id="b90fc-504">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-505">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-506">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-507">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-507">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-508">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-508">15 seconds</span></span> | <span data-ttu-id="b90fc-509">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-510">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-511">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b90fc-512">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="b90fc-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b90fc-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-514">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-514">Option</span></span> | <span data-ttu-id="b90fc-515">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-515">Default value</span></span> | <span data-ttu-id="b90fc-516">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b90fc-517">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-518">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-518">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-519">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b90fc-520">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-521">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b90fc-522">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-523">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-524">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-525">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-526">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-526">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-527">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-527">Option</span></span> | <span data-ttu-id="b90fc-528">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-528">Default value</span></span> | <span data-ttu-id="b90fc-529">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b90fc-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b90fc-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b90fc-531">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-532">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-532">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-533">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-534">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-535">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b90fc-536">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-536">15 seconds</span></span> | <span data-ttu-id="b90fc-537">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-538">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-539">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-540">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-540">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b90fc-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b90fc-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b90fc-542">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-543">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-544">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-545">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b90fc-546">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-546">Configure additional options</span></span>

<span data-ttu-id="b90fc-547">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="b90fc-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-548">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-549">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-549">.NET Option</span></span> |  <span data-ttu-id="b90fc-550">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-550">Default value</span></span> | <span data-ttu-id="b90fc-551">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b90fc-552">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b90fc-553">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-554">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-554">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-555">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-555">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b90fc-556">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-556">Empty</span></span> | <span data-ttu-id="b90fc-557">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b90fc-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b90fc-558">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-558">Empty</span></span> | <span data-ttu-id="b90fc-559">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-559">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b90fc-560">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-560">Empty</span></span> | <span data-ttu-id="b90fc-561">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b90fc-562">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-562">5 seconds</span></span> | <span data-ttu-id="b90fc-563">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-563">WebSockets only.</span></span> <span data-ttu-id="b90fc-564">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b90fc-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b90fc-565">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="b90fc-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b90fc-566">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-566">Empty</span></span> | <span data-ttu-id="b90fc-567">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b90fc-568">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b90fc-569">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="b90fc-570">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b90fc-571">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="b90fc-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b90fc-572">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="b90fc-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b90fc-573">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b90fc-574">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b90fc-575">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="b90fc-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b90fc-576">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b90fc-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b90fc-577">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="b90fc-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b90fc-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-579">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-579">JavaScript Option</span></span> | <span data-ttu-id="b90fc-580">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-580">Default Value</span></span> | <span data-ttu-id="b90fc-581">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b90fc-582">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b90fc-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b90fc-584">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b90fc-585">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-586">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-587">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-588">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-588">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-589">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-589">Java Option</span></span> | <span data-ttu-id="b90fc-590">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-590">Default Value</span></span> | <span data-ttu-id="b90fc-591">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b90fc-592">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b90fc-593">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-594">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-594">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-595">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-595">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b90fc-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b90fc-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b90fc-597">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-597">Empty</span></span> | <span data-ttu-id="b90fc-598">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b90fc-599">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b90fc-600">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b90fc-601">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b90fc-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b90fc-602">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b90fc-603">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-604">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b90fc-604">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b90fc-605">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b90fc-606">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b90fc-607">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-607">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b90fc-608">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b90fc-609">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b90fc-610">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b90fc-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b90fc-611">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="b90fc-612">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b90fc-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b90fc-613">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="b90fc-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b90fc-614">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b90fc-615">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b90fc-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b90fc-616">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b90fc-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b90fc-617">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-617">MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-618">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b90fc-619">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-619">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-620">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b90fc-621">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="b90fc-621">Configure server options</span></span>

<span data-ttu-id="b90fc-622">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="b90fc-622">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b90fc-623">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-623">Option</span></span> | <span data-ttu-id="b90fc-624">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-624">Default Value</span></span> | <span data-ttu-id="b90fc-625">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b90fc-626">30 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-626">30 seconds</span></span> | <span data-ttu-id="b90fc-627">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b90fc-628">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b90fc-629">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b90fc-630">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-630">15 seconds</span></span> | <span data-ttu-id="b90fc-631">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="b90fc-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b90fc-632">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-633">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-633">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-634">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-634">15 seconds</span></span> | <span data-ttu-id="b90fc-635">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b90fc-636">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b90fc-637">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b90fc-638">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="b90fc-638">All installed protocols</span></span> | <span data-ttu-id="b90fc-639">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-639">Protocols supported by this hub.</span></span> <span data-ttu-id="b90fc-640">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b90fc-641">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b90fc-642">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b90fc-643">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b90fc-644">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b90fc-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-645">32 KB</span></span> | <span data-ttu-id="b90fc-646">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b90fc-647">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-647">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b90fc-648">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b90fc-648">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b90fc-649">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="b90fc-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b90fc-650">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b90fc-651">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b90fc-652">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b90fc-652">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b90fc-653">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-653">Option</span></span> | <span data-ttu-id="b90fc-654">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-654">Default Value</span></span> | <span data-ttu-id="b90fc-655">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b90fc-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-656">32 KB</span></span> | <span data-ttu-id="b90fc-657">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b90fc-658">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b90fc-659">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b90fc-660">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b90fc-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-661">32 KB</span></span> | <span data-ttu-id="b90fc-662">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b90fc-663">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b90fc-664">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-664">All Transports are enabled.</span></span> | <span data-ttu-id="b90fc-665">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b90fc-666">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-666">See below.</span></span> | <span data-ttu-id="b90fc-667">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b90fc-668">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-668">See below.</span></span> | <span data-ttu-id="b90fc-669">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b90fc-670">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b90fc-671">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-671">Option</span></span> | <span data-ttu-id="b90fc-672">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-672">Default Value</span></span> | <span data-ttu-id="b90fc-673">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b90fc-674">90 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-674">90 seconds</span></span> | <span data-ttu-id="b90fc-675">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b90fc-676">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b90fc-677">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b90fc-678">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-678">Option</span></span> | <span data-ttu-id="b90fc-679">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-679">Default Value</span></span> | <span data-ttu-id="b90fc-680">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b90fc-681">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-681">5 seconds</span></span> | <span data-ttu-id="b90fc-682">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="b90fc-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b90fc-683">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b90fc-684">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b90fc-685">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="b90fc-685">Configure client options</span></span>

<span data-ttu-id="b90fc-686">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b90fc-687">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b90fc-688">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="b90fc-688">Configure logging</span></span>

<span data-ttu-id="b90fc-689">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b90fc-690">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b90fc-691">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-692">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="b90fc-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b90fc-693">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="b90fc-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b90fc-694">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90fc-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b90fc-695">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b90fc-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b90fc-696">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b90fc-697">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="b90fc-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b90fc-698">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="b90fc-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b90fc-699">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="b90fc-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b90fc-700">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-700">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b90fc-701">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="b90fc-701">The following table lists the available log levels.</span></span> <span data-ttu-id="b90fc-702">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b90fc-703">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-703">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b90fc-704">string</span><span class="sxs-lookup"><span data-stu-id="b90fc-704">String</span></span>                      | <span data-ttu-id="b90fc-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b90fc-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b90fc-706">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="b90fc-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b90fc-707">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="b90fc-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b90fc-708">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b90fc-709">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b90fc-709">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b90fc-710">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-710">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b90fc-711">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="b90fc-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b90fc-712">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="b90fc-712">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b90fc-713">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b90fc-714">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b90fc-715">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="b90fc-715">Configure allowed transports</span></span>

<span data-ttu-id="b90fc-716">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-716">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b90fc-717">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b90fc-718">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b90fc-718">All transports are enabled by default.</span></span>

<span data-ttu-id="b90fc-719">Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="b90fc-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b90fc-720">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b90fc-721">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="b90fc-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b90fc-722">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b90fc-723">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b90fc-724">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-724">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b90fc-725">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="b90fc-725">Configure bearer authentication</span></span>

<span data-ttu-id="b90fc-726">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-726">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b90fc-727">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b90fc-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b90fc-728">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b90fc-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b90fc-729">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b90fc-730">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b90fc-731">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b90fc-732">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b90fc-732">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b90fc-733">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b90fc-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b90fc-734">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b90fc-735">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b90fc-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b90fc-736">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-737">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-738">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-738">Option</span></span> | <span data-ttu-id="b90fc-739">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-739">Default value</span></span> | <span data-ttu-id="b90fc-740">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b90fc-741">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-742">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-742">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-743">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-744">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-745">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-746">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-746">15 seconds</span></span> | <span data-ttu-id="b90fc-747">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-748">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-749">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-750">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-750">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-751">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-751">15 seconds</span></span> | <span data-ttu-id="b90fc-752">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-753">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-754">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b90fc-755">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="b90fc-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b90fc-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-757">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-757">Option</span></span> | <span data-ttu-id="b90fc-758">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-758">Default value</span></span> | <span data-ttu-id="b90fc-759">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b90fc-760">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-761">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-761">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-762">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b90fc-763">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-764">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b90fc-765">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-766">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-767">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-768">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-769">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-769">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-770">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-770">Option</span></span> | <span data-ttu-id="b90fc-771">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-771">Default value</span></span> | <span data-ttu-id="b90fc-772">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b90fc-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b90fc-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b90fc-774">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-775">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-775">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-776">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-777">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-778">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b90fc-779">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-779">15 seconds</span></span> | <span data-ttu-id="b90fc-780">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-781">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-782">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-783">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-783">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b90fc-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b90fc-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b90fc-785">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-786">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-787">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-788">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b90fc-789">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-789">Configure additional options</span></span>

<span data-ttu-id="b90fc-790">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="b90fc-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-791">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-792">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-792">.NET Option</span></span> |  <span data-ttu-id="b90fc-793">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-793">Default value</span></span> | <span data-ttu-id="b90fc-794">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b90fc-795">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b90fc-796">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-797">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-797">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-798">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-798">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b90fc-799">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-799">Empty</span></span> | <span data-ttu-id="b90fc-800">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b90fc-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b90fc-801">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-801">Empty</span></span> | <span data-ttu-id="b90fc-802">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-802">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b90fc-803">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-803">Empty</span></span> | <span data-ttu-id="b90fc-804">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b90fc-805">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-805">5 seconds</span></span> | <span data-ttu-id="b90fc-806">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-806">WebSockets only.</span></span> <span data-ttu-id="b90fc-807">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b90fc-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b90fc-808">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="b90fc-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b90fc-809">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-809">Empty</span></span> | <span data-ttu-id="b90fc-810">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b90fc-811">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b90fc-812">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="b90fc-813">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b90fc-814">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="b90fc-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b90fc-815">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="b90fc-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b90fc-816">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b90fc-817">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b90fc-818">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="b90fc-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b90fc-819">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b90fc-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b90fc-820">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="b90fc-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b90fc-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-822">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-822">JavaScript Option</span></span> | <span data-ttu-id="b90fc-823">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-823">Default Value</span></span> | <span data-ttu-id="b90fc-824">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b90fc-825">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b90fc-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b90fc-827">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b90fc-828">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-829">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-829">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-830">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-830">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-831">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-831">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-832">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-832">Java Option</span></span> | <span data-ttu-id="b90fc-833">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-833">Default Value</span></span> | <span data-ttu-id="b90fc-834">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b90fc-835">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b90fc-836">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-837">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-837">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-838">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-838">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b90fc-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b90fc-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b90fc-840">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-840">Empty</span></span> | <span data-ttu-id="b90fc-841">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b90fc-842">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b90fc-843">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b90fc-844">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b90fc-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b90fc-845">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b90fc-846">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-847">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b90fc-847">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b90fc-848">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b90fc-849">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b90fc-850">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b90fc-851">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b90fc-852">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b90fc-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b90fc-853">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b90fc-854">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b90fc-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b90fc-855">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="b90fc-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b90fc-856">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b90fc-857">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-857">MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-858">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b90fc-859">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-859">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-860">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b90fc-861">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="b90fc-861">Configure server options</span></span>

<span data-ttu-id="b90fc-862">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="b90fc-862">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b90fc-863">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-863">Option</span></span> | <span data-ttu-id="b90fc-864">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-864">Default Value</span></span> | <span data-ttu-id="b90fc-865">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b90fc-866">30 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-866">30 seconds</span></span> | <span data-ttu-id="b90fc-867">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b90fc-868">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b90fc-869">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b90fc-870">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-870">15 seconds</span></span> | <span data-ttu-id="b90fc-871">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="b90fc-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b90fc-872">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-873">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-873">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-874">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-874">15 seconds</span></span> | <span data-ttu-id="b90fc-875">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b90fc-876">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b90fc-877">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b90fc-878">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="b90fc-878">All installed protocols</span></span> | <span data-ttu-id="b90fc-879">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-879">Protocols supported by this hub.</span></span> <span data-ttu-id="b90fc-880">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b90fc-881">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b90fc-882">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b90fc-883">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-883">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b90fc-884">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b90fc-884">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b90fc-885">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="b90fc-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b90fc-886">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b90fc-887">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b90fc-888">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b90fc-888">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b90fc-889">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-889">Option</span></span> | <span data-ttu-id="b90fc-890">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-890">Default Value</span></span> | <span data-ttu-id="b90fc-891">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b90fc-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-892">32 KB</span></span> | <span data-ttu-id="b90fc-893">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="b90fc-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b90fc-894">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b90fc-895">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b90fc-896">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b90fc-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-897">32 KB</span></span> | <span data-ttu-id="b90fc-898">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="b90fc-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b90fc-899">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b90fc-900">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-900">All Transports are enabled.</span></span> | <span data-ttu-id="b90fc-901">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b90fc-902">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-902">See below.</span></span> | <span data-ttu-id="b90fc-903">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b90fc-904">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-904">See below.</span></span> | <span data-ttu-id="b90fc-905">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b90fc-906">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b90fc-907">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-907">Option</span></span> | <span data-ttu-id="b90fc-908">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-908">Default Value</span></span> | <span data-ttu-id="b90fc-909">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b90fc-910">90 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-910">90 seconds</span></span> | <span data-ttu-id="b90fc-911">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b90fc-912">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b90fc-913">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b90fc-914">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-914">Option</span></span> | <span data-ttu-id="b90fc-915">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-915">Default Value</span></span> | <span data-ttu-id="b90fc-916">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b90fc-917">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-917">5 seconds</span></span> | <span data-ttu-id="b90fc-918">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="b90fc-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b90fc-919">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b90fc-920">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b90fc-921">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="b90fc-921">Configure client options</span></span>

<span data-ttu-id="b90fc-922">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b90fc-923">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b90fc-924">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="b90fc-924">Configure logging</span></span>

<span data-ttu-id="b90fc-925">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b90fc-926">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b90fc-927">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-928">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="b90fc-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b90fc-929">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="b90fc-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b90fc-930">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90fc-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b90fc-931">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b90fc-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b90fc-932">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b90fc-933">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="b90fc-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b90fc-934">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="b90fc-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b90fc-935">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b90fc-936">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b90fc-936">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b90fc-937">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-937">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b90fc-938">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="b90fc-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b90fc-939">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="b90fc-939">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b90fc-940">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b90fc-941">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b90fc-942">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="b90fc-942">Configure allowed transports</span></span>

<span data-ttu-id="b90fc-943">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-943">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b90fc-944">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b90fc-945">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b90fc-945">All transports are enabled by default.</span></span>

<span data-ttu-id="b90fc-946">Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="b90fc-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b90fc-947">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b90fc-948">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="b90fc-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b90fc-949">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="b90fc-949">Configure bearer authentication</span></span>

<span data-ttu-id="b90fc-950">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-950">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b90fc-951">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b90fc-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b90fc-952">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b90fc-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b90fc-953">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b90fc-954">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b90fc-955">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b90fc-956">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b90fc-956">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b90fc-957">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b90fc-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b90fc-958">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b90fc-959">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b90fc-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b90fc-960">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-961">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-962">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-962">Option</span></span> | <span data-ttu-id="b90fc-963">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-963">Default value</span></span> | <span data-ttu-id="b90fc-964">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b90fc-965">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-966">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-966">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-967">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-968">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-969">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-970">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-970">15 seconds</span></span> | <span data-ttu-id="b90fc-971">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-972">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-973">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-974">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-974">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-975">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-975">15 seconds</span></span> | <span data-ttu-id="b90fc-976">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-977">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-978">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b90fc-979">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="b90fc-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b90fc-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-981">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-981">Option</span></span> | <span data-ttu-id="b90fc-982">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-982">Default value</span></span> | <span data-ttu-id="b90fc-983">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b90fc-984">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-985">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-985">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-986">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b90fc-987">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-988">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b90fc-989">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-990">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-991">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-992">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-993">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-993">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-994">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-994">Option</span></span> | <span data-ttu-id="b90fc-995">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-995">Default value</span></span> | <span data-ttu-id="b90fc-996">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b90fc-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b90fc-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b90fc-998">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-999">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-999">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-1000">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-1001">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-1002">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b90fc-1003">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1003">15 seconds</span></span> | <span data-ttu-id="b90fc-1004">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-1005">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-1006">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-1007">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1007">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b90fc-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b90fc-1009">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-1010">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b90fc-1011">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b90fc-1012">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b90fc-1013">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-1013">Configure additional options</span></span>

<span data-ttu-id="b90fc-1014">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-1016">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-1016">.NET Option</span></span> |  <span data-ttu-id="b90fc-1017">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1017">Default value</span></span> | <span data-ttu-id="b90fc-1018">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b90fc-1019">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b90fc-1020">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1021">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1021">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1022">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1022">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b90fc-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1023">Empty</span></span> | <span data-ttu-id="b90fc-1024">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b90fc-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1025">Empty</span></span> | <span data-ttu-id="b90fc-1026">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1026">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b90fc-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1027">Empty</span></span> | <span data-ttu-id="b90fc-1028">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b90fc-1029">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1029">5 seconds</span></span> | <span data-ttu-id="b90fc-1030">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1030">WebSockets only.</span></span> <span data-ttu-id="b90fc-1031">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b90fc-1032">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b90fc-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1033">Empty</span></span> | <span data-ttu-id="b90fc-1034">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b90fc-1035">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b90fc-1036">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="b90fc-1037">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b90fc-1038">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b90fc-1039">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="b90fc-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b90fc-1040">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b90fc-1041">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b90fc-1042">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b90fc-1043">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b90fc-1044">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b90fc-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-1046">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-1046">JavaScript Option</span></span> | <span data-ttu-id="b90fc-1047">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1047">Default Value</span></span> | <span data-ttu-id="b90fc-1048">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b90fc-1049">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b90fc-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b90fc-1051">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b90fc-1052">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1053">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1053">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1054">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1054">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-1055">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-1056">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-1056">Java Option</span></span> | <span data-ttu-id="b90fc-1057">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1057">Default Value</span></span> | <span data-ttu-id="b90fc-1058">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b90fc-1059">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b90fc-1060">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1061">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1061">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1062">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1062">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b90fc-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b90fc-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1064">Empty</span></span> | <span data-ttu-id="b90fc-1065">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b90fc-1066">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b90fc-1067">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b90fc-1068">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b90fc-1069">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b90fc-1070">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-1071">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1071">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b90fc-1072">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b90fc-1073">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b90fc-1074">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b90fc-1075">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b90fc-1076">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b90fc-1077">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b90fc-1078">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b90fc-1079">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b90fc-1080">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b90fc-1081">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="b90fc-1081">MessagePack serialization options</span></span>

<span data-ttu-id="b90fc-1082">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b90fc-1083">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1083">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-1084">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b90fc-1085">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="b90fc-1085">Configure server options</span></span>

<span data-ttu-id="b90fc-1086">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1086">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b90fc-1087">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1087">Option</span></span> | <span data-ttu-id="b90fc-1088">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1088">Default Value</span></span> | <span data-ttu-id="b90fc-1089">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-1090">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1090">15 seconds</span></span> | <span data-ttu-id="b90fc-1091">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b90fc-1092">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-1093">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1093">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b90fc-1094">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1094">15 seconds</span></span> | <span data-ttu-id="b90fc-1095">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b90fc-1096">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b90fc-1097">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b90fc-1098">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="b90fc-1098">All installed protocols</span></span> | <span data-ttu-id="b90fc-1099">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="b90fc-1100">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b90fc-1101">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b90fc-1102">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b90fc-1103">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1103">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b90fc-1104">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1104">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b90fc-1105">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="b90fc-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b90fc-1106">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b90fc-1107">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b90fc-1108">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1108">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b90fc-1109">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1109">Option</span></span> | <span data-ttu-id="b90fc-1110">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1110">Default Value</span></span> | <span data-ttu-id="b90fc-1111">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b90fc-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-1112">32 KB</span></span> | <span data-ttu-id="b90fc-1113">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b90fc-1114">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b90fc-1115">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b90fc-1116">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b90fc-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="b90fc-1117">32 KB</span></span> | <span data-ttu-id="b90fc-1118">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b90fc-1119">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b90fc-1120">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1120">All Transports are enabled.</span></span> | <span data-ttu-id="b90fc-1121">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b90fc-1122">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1122">See below.</span></span> | <span data-ttu-id="b90fc-1123">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b90fc-1124">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1124">See below.</span></span> | <span data-ttu-id="b90fc-1125">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b90fc-1126">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b90fc-1127">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1127">Option</span></span> | <span data-ttu-id="b90fc-1128">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1128">Default Value</span></span> | <span data-ttu-id="b90fc-1129">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b90fc-1130">90 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1130">90 seconds</span></span> | <span data-ttu-id="b90fc-1131">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b90fc-1132">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b90fc-1133">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b90fc-1134">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1134">Option</span></span> | <span data-ttu-id="b90fc-1135">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1135">Default Value</span></span> | <span data-ttu-id="b90fc-1136">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b90fc-1137">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1137">5 seconds</span></span> | <span data-ttu-id="b90fc-1138">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b90fc-1139">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b90fc-1140">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b90fc-1141">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="b90fc-1141">Configure client options</span></span>

<span data-ttu-id="b90fc-1142">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b90fc-1143">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b90fc-1144">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1144">Configure logging</span></span>

<span data-ttu-id="b90fc-1145">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b90fc-1146">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b90fc-1147">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b90fc-1148">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b90fc-1149">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b90fc-1150">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b90fc-1151">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b90fc-1152">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b90fc-1153">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b90fc-1154">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b90fc-1155">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b90fc-1156">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1156">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b90fc-1157">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1157">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b90fc-1158">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b90fc-1159">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1159">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b90fc-1160">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b90fc-1161">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b90fc-1162">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="b90fc-1162">Configure allowed transports</span></span>

<span data-ttu-id="b90fc-1163">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1163">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b90fc-1164">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b90fc-1165">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="b90fc-1166">Ad esempio, per disabilitare il trasporto eventi Server-Sent, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b90fc-1167">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b90fc-1168">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="b90fc-1168">Configure bearer authentication</span></span>

<span data-ttu-id="b90fc-1169">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1169">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b90fc-1170">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b90fc-1171">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, in Server-Sent di eventi e richieste WebSocket).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b90fc-1172">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b90fc-1173">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b90fc-1174">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b90fc-1175">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1175">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b90fc-1176">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b90fc-1177">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b90fc-1178">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b90fc-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b90fc-1179">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-1181">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1181">Option</span></span> | <span data-ttu-id="b90fc-1182">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1182">Default value</span></span> | <span data-ttu-id="b90fc-1183">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b90fc-1184">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-1185">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1185">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-1186">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-1187">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-1188">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b90fc-1189">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1189">15 seconds</span></span> | <span data-ttu-id="b90fc-1190">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-1191">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b90fc-1192">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-1193">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1193">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b90fc-1194">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b90fc-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-1196">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1196">Option</span></span> | <span data-ttu-id="b90fc-1197">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1197">Default value</span></span> | <span data-ttu-id="b90fc-1198">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b90fc-1199">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-1200">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1200">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-1201">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b90fc-1202">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-1203">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-1204">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-1205">Opzione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1205">Option</span></span> | <span data-ttu-id="b90fc-1206">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1206">Default value</span></span> | <span data-ttu-id="b90fc-1207">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b90fc-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b90fc-1209">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="b90fc-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b90fc-1210">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1210">Timeout for server activity.</span></span> <span data-ttu-id="b90fc-1211">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-1212">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b90fc-1213">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` , per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b90fc-1214">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1214">15 seconds</span></span> | <span data-ttu-id="b90fc-1215">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="b90fc-1216">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b90fc-1217">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b90fc-1218">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b90fc-1218">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b90fc-1219">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-1219">Configure additional options</span></span>

<span data-ttu-id="b90fc-1220">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="b90fc-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b90fc-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b90fc-1222">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="b90fc-1222">.NET Option</span></span> |  <span data-ttu-id="b90fc-1223">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1223">Default value</span></span> | <span data-ttu-id="b90fc-1224">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b90fc-1225">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b90fc-1226">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1227">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1227">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1228">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1228">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b90fc-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1229">Empty</span></span> | <span data-ttu-id="b90fc-1230">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b90fc-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1231">Empty</span></span> | <span data-ttu-id="b90fc-1232">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1232">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b90fc-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1233">Empty</span></span> | <span data-ttu-id="b90fc-1234">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b90fc-1235">5 secondi</span><span class="sxs-lookup"><span data-stu-id="b90fc-1235">5 seconds</span></span> | <span data-ttu-id="b90fc-1236">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1236">WebSockets only.</span></span> <span data-ttu-id="b90fc-1237">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b90fc-1238">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b90fc-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1239">Empty</span></span> | <span data-ttu-id="b90fc-1240">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b90fc-1241">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b90fc-1242">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="b90fc-1243">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b90fc-1244">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b90fc-1245">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="b90fc-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b90fc-1246">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b90fc-1247">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b90fc-1248">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b90fc-1249">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b90fc-1250">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b90fc-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b90fc-1252">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="b90fc-1252">JavaScript Option</span></span> | <span data-ttu-id="b90fc-1253">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1253">Default Value</span></span> | <span data-ttu-id="b90fc-1254">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b90fc-1255">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="b90fc-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>Valore che specifica il trasporto da utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b90fc-1257">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b90fc-1258">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1259">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1259">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1260">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1260">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b90fc-1261">Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="b90fc-1262">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="b90fc-1262">Java Option</span></span> | <span data-ttu-id="b90fc-1263">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="b90fc-1263">Default Value</span></span> | <span data-ttu-id="b90fc-1264">Descrizione</span><span class="sxs-lookup"><span data-stu-id="b90fc-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b90fc-1265">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b90fc-1266">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b90fc-1267">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1267">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b90fc-1268">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b90fc-1268">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b90fc-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b90fc-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="b90fc-1270">Empty</span></span> | <span data-ttu-id="b90fc-1271">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="b90fc-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b90fc-1272">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b90fc-1273">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b90fc-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b90fc-1274">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b90fc-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b90fc-1275">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="b90fc-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
