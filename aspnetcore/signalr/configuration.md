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
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625557"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="2fbe1-103">Configurazione di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="2fbe1-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2fbe1-104">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-105">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2fbe1-106">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2fbe1-107">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2fbe1-108">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2fbe1-109">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2fbe1-110">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2fbe1-111">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2fbe1-112">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2fbe1-113">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2fbe1-114">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2fbe1-115">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2fbe1-116">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2fbe1-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2fbe1-117">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2fbe1-118">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-118">MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-119">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2fbe1-120">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-121">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2fbe1-122">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="2fbe1-122">Configure server options</span></span>

<span data-ttu-id="2fbe1-123">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2fbe1-124">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-124">Option</span></span> | <span data-ttu-id="2fbe1-125">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-125">Default Value</span></span> | <span data-ttu-id="2fbe1-126">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2fbe1-127">30 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-127">30 seconds</span></span> | <span data-ttu-id="2fbe1-128">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2fbe1-129">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2fbe1-130">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-131">15 seconds</span></span> | <span data-ttu-id="2fbe1-132">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2fbe1-133">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-134">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-135">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-135">15 seconds</span></span> | <span data-ttu-id="2fbe1-136">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2fbe1-137">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2fbe1-138">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2fbe1-139">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="2fbe1-139">All installed protocols</span></span> | <span data-ttu-id="2fbe1-140">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-140">Protocols supported by this hub.</span></span> <span data-ttu-id="2fbe1-141">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2fbe1-142">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2fbe1-143">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2fbe1-144">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2fbe1-145">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2fbe1-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-146">32 KB</span></span> | <span data-ttu-id="2fbe1-147">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2fbe1-148">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2fbe1-149">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2fbe1-150">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="2fbe1-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2fbe1-151">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2fbe1-152">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2fbe1-153">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2fbe1-154">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-154">Option</span></span> | <span data-ttu-id="2fbe1-155">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-155">Default Value</span></span> | <span data-ttu-id="2fbe1-156">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2fbe1-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-157">32 KB</span></span> | <span data-ttu-id="2fbe1-158">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2fbe1-159">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2fbe1-160">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2fbe1-161">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2fbe1-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-162">32 KB</span></span> | <span data-ttu-id="2fbe1-163">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2fbe1-164">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2fbe1-165">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-165">All Transports are enabled.</span></span> | <span data-ttu-id="2fbe1-166">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2fbe1-167">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-167">See below.</span></span> | <span data-ttu-id="2fbe1-168">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2fbe1-169">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-169">See below.</span></span> | <span data-ttu-id="2fbe1-170">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2fbe1-171">0</span><span class="sxs-lookup"><span data-stu-id="2fbe1-171">0</span></span> | <span data-ttu-id="2fbe1-172">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2fbe1-173">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2fbe1-174">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2fbe1-175">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-175">Option</span></span> | <span data-ttu-id="2fbe1-176">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-176">Default Value</span></span> | <span data-ttu-id="2fbe1-177">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2fbe1-178">90 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-178">90 seconds</span></span> | <span data-ttu-id="2fbe1-179">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2fbe1-180">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2fbe1-181">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2fbe1-182">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-182">Option</span></span> | <span data-ttu-id="2fbe1-183">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-183">Default Value</span></span> | <span data-ttu-id="2fbe1-184">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2fbe1-185">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-185">5 seconds</span></span> | <span data-ttu-id="2fbe1-186">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2fbe1-187">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2fbe1-188">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2fbe1-189">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="2fbe1-189">Configure client options</span></span>

<span data-ttu-id="2fbe1-190">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2fbe1-191">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2fbe1-192">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-192">Configure logging</span></span>

<span data-ttu-id="2fbe1-193">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2fbe1-194">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2fbe1-195">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-196">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2fbe1-197">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2fbe1-198">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2fbe1-199">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2fbe1-200">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2fbe1-201">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2fbe1-202">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2fbe1-203">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2fbe1-204">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2fbe1-205">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-205">The following table lists the available log levels.</span></span> <span data-ttu-id="2fbe1-206">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2fbe1-207">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2fbe1-208">string</span><span class="sxs-lookup"><span data-stu-id="2fbe1-208">String</span></span>                      | <span data-ttu-id="2fbe1-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2fbe1-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2fbe1-210">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2fbe1-211">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2fbe1-212">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2fbe1-213">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2fbe1-214">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2fbe1-215">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2fbe1-216">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2fbe1-217">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2fbe1-218">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2fbe1-219">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="2fbe1-219">Configure allowed transports</span></span>

<span data-ttu-id="2fbe1-220">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2fbe1-221">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2fbe1-222">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-222">All transports are enabled by default.</span></span>

<span data-ttu-id="2fbe1-223">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2fbe1-224">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2fbe1-225">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2fbe1-226">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2fbe1-227">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2fbe1-228">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2fbe1-229">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="2fbe1-229">Configure bearer authentication</span></span>

<span data-ttu-id="2fbe1-230">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2fbe1-231">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2fbe1-232">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2fbe1-233">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2fbe1-234">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2fbe1-235">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2fbe1-236">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2fbe1-237">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2fbe1-238">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2fbe1-239">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2fbe1-240">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-241">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-242">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-242">Option</span></span> | <span data-ttu-id="2fbe1-243">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-243">Default value</span></span> | <span data-ttu-id="2fbe1-244">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2fbe1-245">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-246">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-246">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-247">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-248">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-249">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-250">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-250">15 seconds</span></span> | <span data-ttu-id="2fbe1-251">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-252">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-253">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-254">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-255">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-255">15 seconds</span></span> | <span data-ttu-id="2fbe1-256">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-257">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-258">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2fbe1-259">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-261">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-261">Option</span></span> | <span data-ttu-id="2fbe1-262">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-262">Default value</span></span> | <span data-ttu-id="2fbe1-263">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2fbe1-264">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-265">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-265">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-266">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2fbe1-267">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-268">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2fbe1-269">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-270">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-271">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-272">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-273">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-273">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-274">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-274">Option</span></span> | <span data-ttu-id="2fbe1-275">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-275">Default value</span></span> | <span data-ttu-id="2fbe1-276">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2fbe1-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2fbe1-278">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-279">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-279">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-280">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-281">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-282">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2fbe1-283">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-283">15 seconds</span></span> | <span data-ttu-id="2fbe1-284">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-285">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-286">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-287">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2fbe1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2fbe1-289">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-290">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-291">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-292">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2fbe1-293">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-293">Configure additional options</span></span>

<span data-ttu-id="2fbe1-294">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-295">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-296">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-296">.NET Option</span></span> |  <span data-ttu-id="2fbe1-297">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-297">Default value</span></span> | <span data-ttu-id="2fbe1-298">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-299">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2fbe1-300">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-301">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-302">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2fbe1-303">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-303">Empty</span></span> | <span data-ttu-id="2fbe1-304">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2fbe1-305">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-305">Empty</span></span> | <span data-ttu-id="2fbe1-306">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2fbe1-307">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-307">Empty</span></span> | <span data-ttu-id="2fbe1-308">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2fbe1-309">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-309">5 seconds</span></span> | <span data-ttu-id="2fbe1-310">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-310">WebSockets only.</span></span> <span data-ttu-id="2fbe1-311">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2fbe1-312">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2fbe1-313">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-313">Empty</span></span> | <span data-ttu-id="2fbe1-314">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2fbe1-315">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2fbe1-316">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="2fbe1-317">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2fbe1-318">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2fbe1-319">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="2fbe1-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2fbe1-320">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2fbe1-321">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2fbe1-322">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2fbe1-323">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2fbe1-324">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-326">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-326">JavaScript Option</span></span> | <span data-ttu-id="2fbe1-327">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-327">Default Value</span></span> | <span data-ttu-id="2fbe1-328">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2fbe1-329">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="2fbe1-330">Dizionario di intestazioni inviate con tutte le richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="2fbe1-331">L'invio di intestazioni nel browser non funziona per WebSocket o per il <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> flusso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2fbe1-332">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2fbe1-333">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-334">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-335">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="2fbe1-336">Specifica se le credenziali verranno inviate con la richiesta CORS.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="2fbe1-337">App Azure servizio usa cookie per le sessioni permanenti ed è necessario che questa opzione sia abilitata per il corretto funzionamento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="2fbe1-338">Per ulteriori informazioni su CORS con SignalR , vedere <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-339">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-339">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-340">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-340">Java Option</span></span> | <span data-ttu-id="2fbe1-341">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-341">Default Value</span></span> | <span data-ttu-id="2fbe1-342">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-343">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2fbe1-344">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-345">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-346">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2fbe1-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2fbe1-348">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-348">Empty</span></span> | <span data-ttu-id="2fbe1-349">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2fbe1-350">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2fbe1-351">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2fbe1-352">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2fbe1-353">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2fbe1-354">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-355">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2fbe1-356">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2fbe1-357">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2fbe1-358">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2fbe1-359">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2fbe1-360">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2fbe1-361">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2fbe1-362">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2fbe1-363">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2fbe1-364">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2fbe1-365">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2fbe1-366">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2fbe1-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2fbe1-367">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2fbe1-368">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-368">MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-369">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2fbe1-370">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-371">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2fbe1-372">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="2fbe1-372">Configure server options</span></span>

<span data-ttu-id="2fbe1-373">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2fbe1-374">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-374">Option</span></span> | <span data-ttu-id="2fbe1-375">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-375">Default Value</span></span> | <span data-ttu-id="2fbe1-376">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2fbe1-377">30 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-377">30 seconds</span></span> | <span data-ttu-id="2fbe1-378">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2fbe1-379">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2fbe1-380">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-381">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-381">15 seconds</span></span> | <span data-ttu-id="2fbe1-382">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2fbe1-383">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-384">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-385">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-385">15 seconds</span></span> | <span data-ttu-id="2fbe1-386">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2fbe1-387">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2fbe1-388">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2fbe1-389">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="2fbe1-389">All installed protocols</span></span> | <span data-ttu-id="2fbe1-390">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-390">Protocols supported by this hub.</span></span> <span data-ttu-id="2fbe1-391">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2fbe1-392">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2fbe1-393">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2fbe1-394">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2fbe1-395">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2fbe1-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-396">32 KB</span></span> | <span data-ttu-id="2fbe1-397">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2fbe1-398">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2fbe1-399">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2fbe1-400">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="2fbe1-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2fbe1-401">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2fbe1-402">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2fbe1-403">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2fbe1-404">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-404">Option</span></span> | <span data-ttu-id="2fbe1-405">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-405">Default Value</span></span> | <span data-ttu-id="2fbe1-406">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2fbe1-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-407">32 KB</span></span> | <span data-ttu-id="2fbe1-408">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2fbe1-409">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2fbe1-410">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2fbe1-411">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2fbe1-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-412">32 KB</span></span> | <span data-ttu-id="2fbe1-413">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2fbe1-414">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2fbe1-415">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-415">All Transports are enabled.</span></span> | <span data-ttu-id="2fbe1-416">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2fbe1-417">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-417">See below.</span></span> | <span data-ttu-id="2fbe1-418">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2fbe1-419">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-419">See below.</span></span> | <span data-ttu-id="2fbe1-420">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2fbe1-421">0</span><span class="sxs-lookup"><span data-stu-id="2fbe1-421">0</span></span> | <span data-ttu-id="2fbe1-422">Specificare la versione minima del protocollo Negotiate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2fbe1-423">Viene usato per limitare i client a versioni più recenti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2fbe1-424">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2fbe1-425">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-425">Option</span></span> | <span data-ttu-id="2fbe1-426">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-426">Default Value</span></span> | <span data-ttu-id="2fbe1-427">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2fbe1-428">90 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-428">90 seconds</span></span> | <span data-ttu-id="2fbe1-429">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2fbe1-430">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2fbe1-431">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2fbe1-432">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-432">Option</span></span> | <span data-ttu-id="2fbe1-433">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-433">Default Value</span></span> | <span data-ttu-id="2fbe1-434">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2fbe1-435">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-435">5 seconds</span></span> | <span data-ttu-id="2fbe1-436">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2fbe1-437">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2fbe1-438">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2fbe1-439">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="2fbe1-439">Configure client options</span></span>

<span data-ttu-id="2fbe1-440">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2fbe1-441">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2fbe1-442">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-442">Configure logging</span></span>

<span data-ttu-id="2fbe1-443">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2fbe1-444">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2fbe1-445">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-446">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2fbe1-447">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2fbe1-448">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2fbe1-449">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2fbe1-450">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2fbe1-451">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2fbe1-452">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2fbe1-453">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2fbe1-454">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2fbe1-455">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-455">The following table lists the available log levels.</span></span> <span data-ttu-id="2fbe1-456">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2fbe1-457">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2fbe1-458">string</span><span class="sxs-lookup"><span data-stu-id="2fbe1-458">String</span></span>                      | <span data-ttu-id="2fbe1-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2fbe1-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2fbe1-460">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2fbe1-461">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2fbe1-462">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2fbe1-463">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2fbe1-464">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2fbe1-465">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2fbe1-466">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2fbe1-467">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2fbe1-468">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2fbe1-469">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="2fbe1-469">Configure allowed transports</span></span>

<span data-ttu-id="2fbe1-470">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2fbe1-471">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2fbe1-472">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-472">All transports are enabled by default.</span></span>

<span data-ttu-id="2fbe1-473">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2fbe1-474">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2fbe1-475">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2fbe1-476">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2fbe1-477">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2fbe1-478">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2fbe1-479">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="2fbe1-479">Configure bearer authentication</span></span>

<span data-ttu-id="2fbe1-480">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2fbe1-481">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2fbe1-482">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2fbe1-483">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2fbe1-484">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2fbe1-485">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2fbe1-486">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2fbe1-487">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2fbe1-488">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2fbe1-489">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2fbe1-490">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-491">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-492">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-492">Option</span></span> | <span data-ttu-id="2fbe1-493">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-493">Default value</span></span> | <span data-ttu-id="2fbe1-494">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2fbe1-495">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-496">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-496">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-497">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-498">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-499">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-500">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-500">15 seconds</span></span> | <span data-ttu-id="2fbe1-501">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-502">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-503">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-504">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-505">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-505">15 seconds</span></span> | <span data-ttu-id="2fbe1-506">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-507">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-508">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2fbe1-509">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-511">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-511">Option</span></span> | <span data-ttu-id="2fbe1-512">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-512">Default value</span></span> | <span data-ttu-id="2fbe1-513">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2fbe1-514">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-515">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-515">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-516">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2fbe1-517">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-518">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2fbe1-519">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-520">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-521">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-522">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-523">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-523">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-524">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-524">Option</span></span> | <span data-ttu-id="2fbe1-525">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-525">Default value</span></span> | <span data-ttu-id="2fbe1-526">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2fbe1-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2fbe1-528">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-529">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-529">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-530">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-531">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-532">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2fbe1-533">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-533">15 seconds</span></span> | <span data-ttu-id="2fbe1-534">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-535">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-536">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-537">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2fbe1-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2fbe1-539">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-540">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-541">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-542">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2fbe1-543">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-543">Configure additional options</span></span>

<span data-ttu-id="2fbe1-544">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-545">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-546">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-546">.NET Option</span></span> |  <span data-ttu-id="2fbe1-547">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-547">Default value</span></span> | <span data-ttu-id="2fbe1-548">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-549">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2fbe1-550">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-551">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-552">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2fbe1-553">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-553">Empty</span></span> | <span data-ttu-id="2fbe1-554">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2fbe1-555">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-555">Empty</span></span> | <span data-ttu-id="2fbe1-556">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2fbe1-557">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-557">Empty</span></span> | <span data-ttu-id="2fbe1-558">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2fbe1-559">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-559">5 seconds</span></span> | <span data-ttu-id="2fbe1-560">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-560">WebSockets only.</span></span> <span data-ttu-id="2fbe1-561">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2fbe1-562">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2fbe1-563">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-563">Empty</span></span> | <span data-ttu-id="2fbe1-564">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2fbe1-565">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2fbe1-566">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="2fbe1-567">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2fbe1-568">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2fbe1-569">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="2fbe1-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2fbe1-570">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2fbe1-571">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2fbe1-572">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2fbe1-573">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2fbe1-574">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-576">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-576">JavaScript Option</span></span> | <span data-ttu-id="2fbe1-577">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-577">Default Value</span></span> | <span data-ttu-id="2fbe1-578">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2fbe1-579">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2fbe1-580">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2fbe1-581">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-582">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-583">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-584">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-584">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-585">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-585">Java Option</span></span> | <span data-ttu-id="2fbe1-586">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-586">Default Value</span></span> | <span data-ttu-id="2fbe1-587">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-588">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2fbe1-589">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-590">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-591">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2fbe1-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2fbe1-593">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-593">Empty</span></span> | <span data-ttu-id="2fbe1-594">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2fbe1-595">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2fbe1-596">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2fbe1-597">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2fbe1-598">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2fbe1-599">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-600">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2fbe1-601">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2fbe1-602">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2fbe1-603">`AddJsonProtocol`può essere aggiunto dopo [l' SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) di `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2fbe1-604">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2fbe1-605">La proprietà [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) su tale oggetto è un `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> oggetto che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2fbe1-606">Per ulteriori informazioni, vedere la [System.Text.Jssulla documentazione](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2fbe1-607">Ad esempio, per configurare il serializzatore in modo da non modificare la combinazione di maiuscole e minuscole dei nomi di proprietà, anziché i nomi predefiniti "camelCase", usare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="2fbe1-608">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2fbe1-609">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2fbe1-610">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2fbe1-611">Passa a Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2fbe1-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2fbe1-612">Se sono necessarie funzionalità di `Newtonsoft.Json` che non sono supportate in `System.Text.Json` , vedere [passare a Newtonsoft.Js](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2fbe1-613">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-613">MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-614">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2fbe1-615">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-616">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2fbe1-617">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="2fbe1-617">Configure server options</span></span>

<span data-ttu-id="2fbe1-618">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2fbe1-619">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-619">Option</span></span> | <span data-ttu-id="2fbe1-620">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-620">Default Value</span></span> | <span data-ttu-id="2fbe1-621">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2fbe1-622">30 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-622">30 seconds</span></span> | <span data-ttu-id="2fbe1-623">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2fbe1-624">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2fbe1-625">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-626">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-626">15 seconds</span></span> | <span data-ttu-id="2fbe1-627">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2fbe1-628">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-629">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-630">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-630">15 seconds</span></span> | <span data-ttu-id="2fbe1-631">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2fbe1-632">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2fbe1-633">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2fbe1-634">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="2fbe1-634">All installed protocols</span></span> | <span data-ttu-id="2fbe1-635">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-635">Protocols supported by this hub.</span></span> <span data-ttu-id="2fbe1-636">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2fbe1-637">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2fbe1-638">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2fbe1-639">Numero massimo di elementi che possono essere memorizzati nel buffer per i flussi di caricamento client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2fbe1-640">Se viene raggiunto questo limite, l'elaborazione delle chiamate viene bloccata fino a quando il server non elabora gli elementi del flusso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2fbe1-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-641">32 KB</span></span> | <span data-ttu-id="2fbe1-642">Dimensione massima di un singolo messaggio dell'hub in ingresso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2fbe1-643">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2fbe1-644">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2fbe1-645">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="2fbe1-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2fbe1-646">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2fbe1-647">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2fbe1-648">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2fbe1-649">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-649">Option</span></span> | <span data-ttu-id="2fbe1-650">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-650">Default Value</span></span> | <span data-ttu-id="2fbe1-651">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2fbe1-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-652">32 KB</span></span> | <span data-ttu-id="2fbe1-653">Il numero massimo di byte ricevuti dal client che il server memorizza nel buffer prima di applicare la contropressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2fbe1-654">L'aumento di questo valore consente al server di ricevere più rapidamente messaggi di dimensioni maggiori senza applicare la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2fbe1-655">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2fbe1-656">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2fbe1-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-657">32 KB</span></span> | <span data-ttu-id="2fbe1-658">Numero massimo di byte inviati dall'app che il server memorizza nel buffer prima di osservare la sovrapressione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2fbe1-659">L'aumento di questo valore consente al server di memorizzare nel buffer i messaggi di dimensioni maggiori più rapidamente senza attendere la sovrapressione, ma può aumentare l'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2fbe1-660">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-660">All Transports are enabled.</span></span> | <span data-ttu-id="2fbe1-661">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2fbe1-662">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-662">See below.</span></span> | <span data-ttu-id="2fbe1-663">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2fbe1-664">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-664">See below.</span></span> | <span data-ttu-id="2fbe1-665">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2fbe1-666">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2fbe1-667">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-667">Option</span></span> | <span data-ttu-id="2fbe1-668">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-668">Default Value</span></span> | <span data-ttu-id="2fbe1-669">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2fbe1-670">90 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-670">90 seconds</span></span> | <span data-ttu-id="2fbe1-671">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2fbe1-672">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2fbe1-673">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2fbe1-674">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-674">Option</span></span> | <span data-ttu-id="2fbe1-675">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-675">Default Value</span></span> | <span data-ttu-id="2fbe1-676">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2fbe1-677">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-677">5 seconds</span></span> | <span data-ttu-id="2fbe1-678">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2fbe1-679">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2fbe1-680">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2fbe1-681">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="2fbe1-681">Configure client options</span></span>

<span data-ttu-id="2fbe1-682">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2fbe1-683">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2fbe1-684">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-684">Configure logging</span></span>

<span data-ttu-id="2fbe1-685">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2fbe1-686">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2fbe1-687">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-688">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2fbe1-689">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2fbe1-690">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2fbe1-691">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2fbe1-692">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2fbe1-693">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2fbe1-694">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2fbe1-695">Anziché un `LogLevel` valore, è anche possibile fornire un `string` valore che rappresenta il nome di un livello di log.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2fbe1-696">Questa operazione è utile quando SignalR si configura la registrazione in ambienti in cui non è possibile accedere alle `LogLevel` costanti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2fbe1-697">Nella tabella seguente sono elencati i livelli di log disponibili.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-697">The following table lists the available log levels.</span></span> <span data-ttu-id="2fbe1-698">Il valore fornito per `configureLogging` impostare il livello di registrazione **minimo** che verrà registrato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2fbe1-699">Verranno registrati i messaggi registrati a questo livello **o i livelli elencati dopo tale operazione nella tabella**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2fbe1-700">string</span><span class="sxs-lookup"><span data-stu-id="2fbe1-700">String</span></span>                      | <span data-ttu-id="2fbe1-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2fbe1-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2fbe1-702">`info`**o**`information`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2fbe1-703">`warn`**o**`warning`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2fbe1-704">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2fbe1-705">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2fbe1-706">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2fbe1-707">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2fbe1-708">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2fbe1-709">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2fbe1-710">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2fbe1-711">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="2fbe1-711">Configure allowed transports</span></span>

<span data-ttu-id="2fbe1-712">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2fbe1-713">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2fbe1-714">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-714">All transports are enabled by default.</span></span>

<span data-ttu-id="2fbe1-715">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2fbe1-716">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2fbe1-717">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2fbe1-718">Nel client Java il trasporto viene selezionato con il `withTransport` metodo in `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2fbe1-719">Per impostazione predefinita, il client Java usa il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2fbe1-720">Il SignalR client Java non supporta ancora il fallback del trasporto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2fbe1-721">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="2fbe1-721">Configure bearer authentication</span></span>

<span data-ttu-id="2fbe1-722">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2fbe1-723">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2fbe1-724">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2fbe1-725">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2fbe1-726">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2fbe1-727">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2fbe1-728">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2fbe1-729">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2fbe1-730">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2fbe1-731">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2fbe1-732">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-733">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-734">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-734">Option</span></span> | <span data-ttu-id="2fbe1-735">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-735">Default value</span></span> | <span data-ttu-id="2fbe1-736">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2fbe1-737">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-738">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-738">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-739">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-740">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-741">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-742">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-742">15 seconds</span></span> | <span data-ttu-id="2fbe1-743">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-744">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-745">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-746">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-747">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-747">15 seconds</span></span> | <span data-ttu-id="2fbe1-748">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-749">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-750">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2fbe1-751">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-753">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-753">Option</span></span> | <span data-ttu-id="2fbe1-754">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-754">Default value</span></span> | <span data-ttu-id="2fbe1-755">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2fbe1-756">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-757">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-757">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-758">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2fbe1-759">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-760">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2fbe1-761">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-762">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-763">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-764">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-765">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-765">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-766">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-766">Option</span></span> | <span data-ttu-id="2fbe1-767">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-767">Default value</span></span> | <span data-ttu-id="2fbe1-768">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2fbe1-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2fbe1-770">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-771">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-771">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-772">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-773">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-774">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2fbe1-775">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-775">15 seconds</span></span> | <span data-ttu-id="2fbe1-776">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-777">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-778">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-779">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2fbe1-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2fbe1-781">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-782">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-783">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-784">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2fbe1-785">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-785">Configure additional options</span></span>

<span data-ttu-id="2fbe1-786">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-787">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-788">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-788">.NET Option</span></span> |  <span data-ttu-id="2fbe1-789">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-789">Default value</span></span> | <span data-ttu-id="2fbe1-790">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-791">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2fbe1-792">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-793">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-794">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2fbe1-795">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-795">Empty</span></span> | <span data-ttu-id="2fbe1-796">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2fbe1-797">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-797">Empty</span></span> | <span data-ttu-id="2fbe1-798">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2fbe1-799">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-799">Empty</span></span> | <span data-ttu-id="2fbe1-800">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2fbe1-801">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-801">5 seconds</span></span> | <span data-ttu-id="2fbe1-802">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-802">WebSockets only.</span></span> <span data-ttu-id="2fbe1-803">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2fbe1-804">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2fbe1-805">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-805">Empty</span></span> | <span data-ttu-id="2fbe1-806">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2fbe1-807">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2fbe1-808">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="2fbe1-809">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2fbe1-810">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2fbe1-811">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="2fbe1-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2fbe1-812">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2fbe1-813">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2fbe1-814">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2fbe1-815">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2fbe1-816">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-818">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-818">JavaScript Option</span></span> | <span data-ttu-id="2fbe1-819">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-819">Default Value</span></span> | <span data-ttu-id="2fbe1-820">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2fbe1-821">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2fbe1-822">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2fbe1-823">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-824">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-825">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-826">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-826">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-827">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-827">Java Option</span></span> | <span data-ttu-id="2fbe1-828">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-828">Default Value</span></span> | <span data-ttu-id="2fbe1-829">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-830">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2fbe1-831">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-832">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-833">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2fbe1-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2fbe1-835">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-835">Empty</span></span> | <span data-ttu-id="2fbe1-836">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2fbe1-837">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2fbe1-838">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2fbe1-839">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2fbe1-840">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2fbe1-841">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-842">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2fbe1-843">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2fbe1-844">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2fbe1-845">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2fbe1-846">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2fbe1-847">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2fbe1-848">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2fbe1-849">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2fbe1-850">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2fbe1-851">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2fbe1-852">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-852">MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-853">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2fbe1-854">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-855">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2fbe1-856">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="2fbe1-856">Configure server options</span></span>

<span data-ttu-id="2fbe1-857">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2fbe1-858">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-858">Option</span></span> | <span data-ttu-id="2fbe1-859">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-859">Default Value</span></span> | <span data-ttu-id="2fbe1-860">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2fbe1-861">30 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-861">30 seconds</span></span> | <span data-ttu-id="2fbe1-862">Il server prenderà in considerazione il client disconnesso se non ha ricevuto un messaggio (incluso Keep-Alive) in questo intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2fbe1-863">Potrebbe essere necessario più tempo di questo intervallo di timeout affinché il client venga effettivamente contrassegnato come disconnesso, a causa della modalità di implementazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2fbe1-864">Il valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-865">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-865">15 seconds</span></span> | <span data-ttu-id="2fbe1-866">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2fbe1-867">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-868">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-869">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-869">15 seconds</span></span> | <span data-ttu-id="2fbe1-870">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2fbe1-871">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2fbe1-872">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2fbe1-873">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="2fbe1-873">All installed protocols</span></span> | <span data-ttu-id="2fbe1-874">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-874">Protocols supported by this hub.</span></span> <span data-ttu-id="2fbe1-875">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2fbe1-876">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2fbe1-877">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2fbe1-878">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2fbe1-879">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2fbe1-880">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="2fbe1-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2fbe1-881">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2fbe1-882">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2fbe1-883">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2fbe1-884">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-884">Option</span></span> | <span data-ttu-id="2fbe1-885">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-885">Default Value</span></span> | <span data-ttu-id="2fbe1-886">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2fbe1-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-887">32 KB</span></span> | <span data-ttu-id="2fbe1-888">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2fbe1-889">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2fbe1-890">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2fbe1-891">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2fbe1-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-892">32 KB</span></span> | <span data-ttu-id="2fbe1-893">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2fbe1-894">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2fbe1-895">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-895">All Transports are enabled.</span></span> | <span data-ttu-id="2fbe1-896">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2fbe1-897">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-897">See below.</span></span> | <span data-ttu-id="2fbe1-898">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2fbe1-899">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-899">See below.</span></span> | <span data-ttu-id="2fbe1-900">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2fbe1-901">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2fbe1-902">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-902">Option</span></span> | <span data-ttu-id="2fbe1-903">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-903">Default Value</span></span> | <span data-ttu-id="2fbe1-904">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2fbe1-905">90 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-905">90 seconds</span></span> | <span data-ttu-id="2fbe1-906">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2fbe1-907">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2fbe1-908">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2fbe1-909">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-909">Option</span></span> | <span data-ttu-id="2fbe1-910">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-910">Default Value</span></span> | <span data-ttu-id="2fbe1-911">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2fbe1-912">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-912">5 seconds</span></span> | <span data-ttu-id="2fbe1-913">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2fbe1-914">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2fbe1-915">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2fbe1-916">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="2fbe1-916">Configure client options</span></span>

<span data-ttu-id="2fbe1-917">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2fbe1-918">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2fbe1-919">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-919">Configure logging</span></span>

<span data-ttu-id="2fbe1-920">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2fbe1-921">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2fbe1-922">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-923">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2fbe1-924">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2fbe1-925">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2fbe1-926">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2fbe1-927">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2fbe1-928">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2fbe1-929">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2fbe1-930">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2fbe1-931">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2fbe1-932">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2fbe1-933">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2fbe1-934">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2fbe1-935">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2fbe1-936">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2fbe1-937">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="2fbe1-937">Configure allowed transports</span></span>

<span data-ttu-id="2fbe1-938">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2fbe1-939">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2fbe1-940">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-940">All transports are enabled by default.</span></span>

<span data-ttu-id="2fbe1-941">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2fbe1-942">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2fbe1-943">In questa versione dei WebSocket client Java è l'unico trasporto disponibile.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2fbe1-944">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="2fbe1-944">Configure bearer authentication</span></span>

<span data-ttu-id="2fbe1-945">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2fbe1-946">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2fbe1-947">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2fbe1-948">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2fbe1-949">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2fbe1-950">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2fbe1-951">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2fbe1-952">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2fbe1-953">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2fbe1-954">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2fbe1-955">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-956">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-957">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-957">Option</span></span> | <span data-ttu-id="2fbe1-958">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-958">Default value</span></span> | <span data-ttu-id="2fbe1-959">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2fbe1-960">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-961">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-961">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-962">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-963">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-964">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-965">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-965">15 seconds</span></span> | <span data-ttu-id="2fbe1-966">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-967">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-968">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-969">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-970">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-970">15 seconds</span></span> | <span data-ttu-id="2fbe1-971">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-972">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-973">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2fbe1-974">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-976">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-976">Option</span></span> | <span data-ttu-id="2fbe1-977">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-977">Default value</span></span> | <span data-ttu-id="2fbe1-978">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2fbe1-979">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-980">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-980">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-981">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2fbe1-982">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-983">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2fbe1-984">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-985">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-986">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-987">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-988">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-988">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-989">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-989">Option</span></span> | <span data-ttu-id="2fbe1-990">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-990">Default value</span></span> | <span data-ttu-id="2fbe1-991">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2fbe1-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2fbe1-993">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-994">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-994">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-995">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-996">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-997">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2fbe1-998">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-998">15 seconds</span></span> | <span data-ttu-id="2fbe1-999">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-1000">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-1001">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-1002">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2fbe1-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2fbe1-1004">15 secondi (15.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-1005">Determina l'intervallo in corrispondenza del quale il client invia messaggi ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2fbe1-1006">L'invio di messaggi dal client Reimposta il timer all'inizio dell'intervallo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2fbe1-1007">Se il client non ha inviato un messaggio nel `ClientTimeoutInterval` set nel server, il server considera il client disconnesso.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2fbe1-1008">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1008">Configure additional options</span></span>

<span data-ttu-id="2fbe1-1009">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-1011">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1011">.NET Option</span></span> |  <span data-ttu-id="2fbe1-1012">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1012">Default value</span></span> | <span data-ttu-id="2fbe1-1013">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-1014">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2fbe1-1015">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1016">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1017">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2fbe1-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1018">Empty</span></span> | <span data-ttu-id="2fbe1-1019">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2fbe1-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1020">Empty</span></span> | <span data-ttu-id="2fbe1-1021">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2fbe1-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1022">Empty</span></span> | <span data-ttu-id="2fbe1-1023">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2fbe1-1024">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1024">5 seconds</span></span> | <span data-ttu-id="2fbe1-1025">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1025">WebSockets only.</span></span> <span data-ttu-id="2fbe1-1026">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2fbe1-1027">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2fbe1-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1028">Empty</span></span> | <span data-ttu-id="2fbe1-1029">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2fbe1-1030">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2fbe1-1031">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="2fbe1-1032">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2fbe1-1033">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2fbe1-1034">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2fbe1-1035">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2fbe1-1036">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2fbe1-1037">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2fbe1-1038">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2fbe1-1039">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-1041">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1041">JavaScript Option</span></span> | <span data-ttu-id="2fbe1-1042">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1042">Default Value</span></span> | <span data-ttu-id="2fbe1-1043">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2fbe1-1044">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2fbe1-1045">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2fbe1-1046">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1047">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1048">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-1049">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-1050">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1050">Java Option</span></span> | <span data-ttu-id="2fbe1-1051">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1051">Default Value</span></span> | <span data-ttu-id="2fbe1-1052">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-1053">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2fbe1-1054">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1055">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1056">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2fbe1-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2fbe1-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1058">Empty</span></span> | <span data-ttu-id="2fbe1-1059">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2fbe1-1060">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2fbe1-1061">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2fbe1-1062">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2fbe1-1063">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2fbe1-1064">Opzioni di serializzazione JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-1065">ASP.NET Core SignalR supporta due protocolli per la codifica dei messaggi: [JSON](https://www.json.org/) e [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2fbe1-1066">Ogni protocollo dispone di opzioni di configurazione della serializzazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2fbe1-1067">La serializzazione JSON può essere configurata nel server usando il metodo di estensione [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , che può essere aggiunto dopo l' [ SignalR aggiunta](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) nel `Startup.ConfigureServices` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2fbe1-1068">Il `AddJsonProtocol` metodo accetta un delegato che riceve un `options` oggetto.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2fbe1-1069">La proprietà [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) su tale oggetto è un `JsonSerializerSettings` oggetto JSON.NET che può essere utilizzato per configurare la serializzazione di argomenti e valori restituiti.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2fbe1-1070">Per ulteriori informazioni, vedere la [documentazione di JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2fbe1-1071">Per configurare, ad esempio, il serializzatore per l'utilizzo di nomi di proprietà "PascalCase", anziché i nomi predefiniti "camelCase", utilizzare il codice seguente in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2fbe1-1072">Nel client .NET lo stesso metodo di `AddJsonProtocol` estensione è presente in [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2fbe1-1073">`Microsoft.Extensions.DependencyInjection`Per risolvere il metodo di estensione, è necessario importare lo spazio dei nomi:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2fbe1-1074">Al momento non è possibile configurare la serializzazione JSON nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2fbe1-1075">Opzioni di serializzazione MessagePack</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1075">MessagePack serialization options</span></span>

<span data-ttu-id="2fbe1-1076">La serializzazione MessagePack può essere configurata fornendo un delegato alla chiamata [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2fbe1-1077">Per ulteriori informazioni, vedere [MessagePack in SignalR ](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-1078">Al momento non è possibile configurare la serializzazione MessagePack nel client JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2fbe1-1079">Configurare le opzioni del server</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1079">Configure server options</span></span>

<span data-ttu-id="2fbe1-1080">La tabella seguente descrive le opzioni per la configurazione degli SignalR Hub:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2fbe1-1081">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1081">Option</span></span> | <span data-ttu-id="2fbe1-1082">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1082">Default Value</span></span> | <span data-ttu-id="2fbe1-1083">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-1084">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1084">15 seconds</span></span> | <span data-ttu-id="2fbe1-1085">Se il client non invia un messaggio di handshake iniziale entro questo intervallo di tempo, la connessione viene chiusa.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2fbe1-1086">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-1087">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2fbe1-1088">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1088">15 seconds</span></span> | <span data-ttu-id="2fbe1-1089">Se il server non ha inviato un messaggio entro questo intervallo, viene inviato automaticamente un messaggio di ping per lasciare aperta la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2fbe1-1090">Quando si modifica `KeepAliveInterval` , modificare l' `ServerTimeout` / `serverTimeoutInMilliseconds` impostazione nel client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2fbe1-1091">Il `ServerTimeout` / `serverTimeoutInMilliseconds` valore consigliato è Double `KeepAliveInterval` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2fbe1-1092">Tutti i protocolli installati</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1092">All installed protocols</span></span> | <span data-ttu-id="2fbe1-1093">Protocolli supportati da questo hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="2fbe1-1094">Per impostazione predefinita, tutti i protocolli registrati nel server sono consentiti, ma i protocolli possono essere rimossi da questo elenco per disabilitare protocolli specifici per i singoli Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2fbe1-1095">Se `true` , i messaggi di eccezione dettagliati vengono restituiti ai client quando viene generata un'eccezione in un metodo dell'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2fbe1-1096">Il valore predefinito è `false` , poiché questi messaggi di eccezione possono contenere informazioni riservate.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2fbe1-1097">È possibile configurare le opzioni per tutti gli hub fornendo le opzioni delegate alla `AddSignalR` chiamata in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2fbe1-1098">Le opzioni per un singolo Hub eseguono l'override delle opzioni globali fornite in `AddSignalR` e possono essere configurate utilizzando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2fbe1-1099">Opzioni di configurazione HTTP avanzate</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2fbe1-1100">Utilizzare `HttpConnectionDispatcherOptions` per configurare le impostazioni avanzate relative ai trasporti e alla gestione dei buffer di memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2fbe1-1101">Queste opzioni vengono configurate passando un delegato [a \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2fbe1-1102">Nella tabella seguente vengono descritte le opzioni per la configurazione delle SignalR Opzioni http avanzate di ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2fbe1-1103">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1103">Option</span></span> | <span data-ttu-id="2fbe1-1104">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1104">Default Value</span></span> | <span data-ttu-id="2fbe1-1105">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2fbe1-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1106">32 KB</span></span> | <span data-ttu-id="2fbe1-1107">Numero massimo di byte ricevuti dal client che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2fbe1-1108">L'aumento di questo valore consente al server di ricevere messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2fbe1-1109">Dati raccolti automaticamente dagli `Authorize` attributi applicati alla classe Hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2fbe1-1110">Elenco di oggetti [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) usati per determinare se un client è autorizzato a connettersi all'hub.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2fbe1-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1111">32 KB</span></span> | <span data-ttu-id="2fbe1-1112">Numero massimo di byte inviati dall'app che il server memorizza nel buffer.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2fbe1-1113">L'aumento di questo valore consente al server di inviare messaggi di dimensioni maggiori, ma può influire negativamente sull'utilizzo della memoria.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2fbe1-1114">Tutti i trasporti sono abilitati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1114">All Transports are enabled.</span></span> | <span data-ttu-id="2fbe1-1115">Enumerazione dei flag di bit di `HttpTransportType` valori che possono limitare i trasporti che un client può utilizzare per la connessione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2fbe1-1116">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1116">See below.</span></span> | <span data-ttu-id="2fbe1-1117">Opzioni aggiuntive specifiche per il trasporto di polling lungo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2fbe1-1118">Vedere qui di seguito.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1118">See below.</span></span> | <span data-ttu-id="2fbe1-1119">Opzioni aggiuntive specifiche per il trasporto WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2fbe1-1120">Il trasporto di polling lungo dispone di opzioni aggiuntive che possono essere configurate tramite la `LongPolling` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2fbe1-1121">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1121">Option</span></span> | <span data-ttu-id="2fbe1-1122">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1122">Default Value</span></span> | <span data-ttu-id="2fbe1-1123">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2fbe1-1124">90 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1124">90 seconds</span></span> | <span data-ttu-id="2fbe1-1125">Quantità massima di tempo di attesa del server per l'invio di un messaggio al client prima che venga terminata una singola richiesta di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2fbe1-1126">La riduzione di questo valore determina la frequenza con cui il client rilascia più richieste di polling.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2fbe1-1127">Il trasporto WebSocket dispone di opzioni aggiuntive che possono essere configurate tramite la `WebSockets` proprietà:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2fbe1-1128">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1128">Option</span></span> | <span data-ttu-id="2fbe1-1129">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1129">Default Value</span></span> | <span data-ttu-id="2fbe1-1130">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2fbe1-1131">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1131">5 seconds</span></span> | <span data-ttu-id="2fbe1-1132">Dopo la chiusura del server, se il client non riesce a chiudersi entro questo intervallo di tempo, la connessione viene terminata.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2fbe1-1133">Delegato che può essere utilizzato per impostare l' `Sec-WebSocket-Protocol` intestazione su un valore personalizzato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2fbe1-1134">Il delegato riceve i valori richiesti dal client come input ed è previsto che restituisca il valore desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2fbe1-1135">Configurare le opzioni client</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1135">Configure client options</span></span>

<span data-ttu-id="2fbe1-1136">Le opzioni client possono essere configurate nel `HubConnectionBuilder` tipo, disponibile nei client .NET e JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2fbe1-1137">È anche disponibile nel client Java, ma la `HttpHubConnectionBuilder` sottoclasse è quella che contiene le opzioni di configurazione del compilatore, oltre a quella di `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2fbe1-1138">Configurare la registrazione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1138">Configure logging</span></span>

<span data-ttu-id="2fbe1-1139">La registrazione viene configurata nel client .NET utilizzando il `ConfigureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2fbe1-1140">I provider di registrazione e i filtri possono essere registrati nello stesso modo in cui si trovano nel server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2fbe1-1141">Per ulteriori informazioni, vedere la documentazione relativa all' [accesso ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2fbe1-1142">Per registrare i provider di registrazione, è necessario installare i pacchetti necessari.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2fbe1-1143">Per un elenco completo, vedere la sezione [provider di registrazione incorporati](xref:fundamentals/logging/index#built-in-logging-providers) di docs.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2fbe1-1144">Ad esempio, per abilitare la registrazione della console, installare il `Microsoft.Extensions.Logging.Console` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2fbe1-1145">Chiamare il `AddConsole` metodo di estensione:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2fbe1-1146">Nel client JavaScript esiste un metodo simile `configureLogging` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2fbe1-1147">Fornire un `LogLevel` valore che indichi il livello minimo di messaggi di log da produrre.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2fbe1-1148">I log vengono scritti nella finestra della console del browser.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2fbe1-1149">Per disabilitare completamente la registrazione, specificare `signalR.LogLevel.None` nel `configureLogging` metodo.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2fbe1-1150">Per ulteriori informazioni sulla registrazione, vedere la [ SignalR documentazione di diagnostica](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2fbe1-1151">Il SignalR client Java usa la libreria [SLF4J](https://www.slf4j.org/) per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2fbe1-1152">Si tratta di un'API di registrazione di alto livello che consente agli utenti della libreria di scegliere una propria implementazione di registrazione specifica inserendo una dipendenza di registrazione specifica.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2fbe1-1153">Il frammento di codice seguente illustra come usare `java.util.logging` con il SignalR client Java.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2fbe1-1154">Se non si configura la registrazione nelle dipendenze, SLF4J carica un logger senza operazioni predefinito con il seguente messaggio di avviso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2fbe1-1155">Questa operazione può essere ignorata in modo sicuro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2fbe1-1156">Configurare i trasporti consentiti</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1156">Configure allowed transports</span></span>

<span data-ttu-id="2fbe1-1157">I trasporti utilizzati da SignalR possono essere configurati nella `WithUrl` chiamata ( `withUrl` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2fbe1-1158">Un operatore OR bit per bit dei valori di `HttpTransportType` può essere utilizzato per limitare il client in modo che utilizzi solo i trasporti specificati.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2fbe1-1159">Tutti i trasporti sono abilitati per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="2fbe1-1160">Ad esempio, per disabilitare il trasporto degli eventi inviati dal server, ma consentire i WebSocket e le connessioni di polling lungo:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2fbe1-1161">Nel client JavaScript, i trasporti vengono configurati impostando il `transport` campo nell'oggetto opzioni fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2fbe1-1162">Configurare l'autenticazione della porta</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1162">Configure bearer authentication</span></span>

<span data-ttu-id="2fbe1-1163">Per fornire i dati di autenticazione insieme alle SignalR richieste, usare l' `AccessTokenProvider` opzione ( `accessTokenFactory` in JavaScript) per specificare una funzione che restituisce il token di accesso desiderato.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2fbe1-1164">Nel client .NET questo token di accesso viene passato come token HTTP "Bearer Authentication" (usando l' `Authorization` intestazione con un tipo di `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2fbe1-1165">Nel client JavaScript, il token di accesso viene usato come token di connessione, **ad eccezione** dei casi in cui le API del browser limitano la possibilità di applicare intestazioni (in particolare, nelle richieste di eventi inviati dal server e WebSocket).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2fbe1-1166">In questi casi, il token di accesso viene fornito come valore stringa di query `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2fbe1-1167">Nel client .NET `AccessTokenProvider` è possibile specificare l'opzione usando il delegato options in `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2fbe1-1168">Nel client JavaScript, il token di accesso viene configurato impostando il `accessTokenFactory` campo nell'oggetto opzioni in `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2fbe1-1169">Nel SignalR client Java è possibile configurare un Bearer token da usare per l'autenticazione fornendo una factory del token di accesso a [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2fbe1-1170">Usare [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) per fornire un [RxJava](https://github.com/ReactiveX/RxJava) [singolo \<String> ](https://reactivex.io/documentation/single.html)RxJava.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2fbe1-1171">Con una chiamata a [Single. rinvia](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), è possibile scrivere la logica per produrre i token di accesso per il client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2fbe1-1172">Configurare le opzioni di timeout e Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2fbe1-1173">Opzioni aggiuntive per la configurazione del timeout e del comportamento keep-alive sono disponibili nell' `HubConnection` oggetto stesso:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-1175">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1175">Option</span></span> | <span data-ttu-id="2fbe1-1176">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1176">Default value</span></span> | <span data-ttu-id="2fbe1-1177">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2fbe1-1178">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-1179">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1179">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-1180">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-1181">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-1182">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2fbe1-1183">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1183">15 seconds</span></span> | <span data-ttu-id="2fbe1-1184">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-1185">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `Closed` evento ( `onclose` in JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2fbe1-1186">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-1187">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="2fbe1-1188">Nel client .NET i valori di timeout vengono specificati come `TimeSpan` valori.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-1190">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1190">Option</span></span> | <span data-ttu-id="2fbe1-1191">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1191">Default value</span></span> | <span data-ttu-id="2fbe1-1192">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2fbe1-1193">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-1194">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1194">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-1195">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onclose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2fbe1-1196">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-1197">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-1198">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-1199">Opzione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1199">Option</span></span> | <span data-ttu-id="2fbe1-1200">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1200">Default value</span></span> | <span data-ttu-id="2fbe1-1201">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2fbe1-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2fbe1-1203">30 secondi (30.000 millisecondi)</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2fbe1-1204">Timeout per l'attività del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1204">Timeout for server activity.</span></span> <span data-ttu-id="2fbe1-1205">Se il server non ha inviato un messaggio in questo intervallo, il client considera il server disconnesso e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-1206">Questo valore deve essere sufficientemente grande da consentire l'invio del messaggio ping dal server **e** la ricezione da parte del client entro l'intervallo di timeout.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2fbe1-1207">Il valore consigliato è un numero almeno il doppio del valore del server `KeepAliveInterval` , per consentire l'arrivo dei ping.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2fbe1-1208">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1208">15 seconds</span></span> | <span data-ttu-id="2fbe1-1209">Timeout per l'handshake iniziale del server.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="2fbe1-1210">Se il server non invia una risposta di handshake in questo intervallo, il client Annulla l'handshake e attiva l' `onClose` evento.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2fbe1-1211">Si tratta di un'impostazione avanzata che deve essere modificata solo se si verificano errori di timeout dell'handshake a causa di una latenza di rete grave.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2fbe1-1212">Per informazioni dettagliate sul processo di handshake, vedere la [ SignalR specifica del protocollo Hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2fbe1-1213">Configurare opzioni aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1213">Configure additional options</span></span>

<span data-ttu-id="2fbe1-1214">Opzioni aggiuntive possono essere configurate `WithUrl` nel `withUrl` Metodo (in JavaScript) in `HubConnectionBuilder` o nelle varie API di configurazione di `HttpHubConnectionBuilder` nel client Java:</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2fbe1-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2fbe1-1216">Opzione .NET</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1216">.NET Option</span></span> |  <span data-ttu-id="2fbe1-1217">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1217">Default value</span></span> | <span data-ttu-id="2fbe1-1218">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-1219">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2fbe1-1220">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1221">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1222">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2fbe1-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1223">Empty</span></span> | <span data-ttu-id="2fbe1-1224">Raccolta di certificati TLS da inviare per autenticare le richieste.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2fbe1-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1225">Empty</span></span> | <span data-ttu-id="2fbe1-1226">Raccolta di HTTP cookie s da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2fbe1-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1227">Empty</span></span> | <span data-ttu-id="2fbe1-1228">Credenziali da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2fbe1-1229">5 secondi</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1229">5 seconds</span></span> | <span data-ttu-id="2fbe1-1230">Solo WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1230">WebSockets only.</span></span> <span data-ttu-id="2fbe1-1231">Quantità massima di tempo di attesa del client dopo la chiusura del server per confermare la richiesta di chiusura.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2fbe1-1232">Se il server non riconosce la chiusura entro questo intervallo di tempo, il client si disconnette.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2fbe1-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1233">Empty</span></span> | <span data-ttu-id="2fbe1-1234">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2fbe1-1235">Delegato che può essere utilizzato per configurare o sostituire l'oggetto `HttpMessageHandler` utilizzato per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2fbe1-1236">Non usato per le connessioni WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="2fbe1-1237">Questo delegato deve restituire un valore non null e riceve il valore predefinito come parametro.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2fbe1-1238">Modificare le impostazioni del valore predefinito e restituirlo oppure restituire una nuova `HttpMessageHandler` istanza.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2fbe1-1239">**Quando si sostituisce il gestore, assicurarsi di copiare le impostazioni che si vuole usare dal gestore fornito. in caso contrario, le opzioni configurate, ad esempio le Cookie e le intestazioni, non verranno applicate al nuovo gestore.**</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2fbe1-1240">Proxy HTTP da usare per l'invio di richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2fbe1-1241">Impostare questo valore booleano per inviare le credenziali predefinite per le richieste HTTP e WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2fbe1-1242">Questo consente l'uso dell'autenticazione di Windows.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2fbe1-1243">Delegato che può essere usato per configurare opzioni WebSocket aggiuntive.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2fbe1-1244">Riceve un'istanza di [Metodo ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) che può essere utilizzata per configurare le opzioni.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2fbe1-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2fbe1-1246">Opzione JavaScript</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1246">JavaScript Option</span></span> | <span data-ttu-id="2fbe1-1247">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1247">Default Value</span></span> | <span data-ttu-id="2fbe1-1248">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2fbe1-1249">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2fbe1-1250">Impostare su `true` per registrare i byte/caratteri dei messaggi inviati e ricevuti dal client.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2fbe1-1251">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1252">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1253">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2fbe1-1254">Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="2fbe1-1255">Opzione Java</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1255">Java Option</span></span> | <span data-ttu-id="2fbe1-1256">Valore predefinito</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1256">Default Value</span></span> | <span data-ttu-id="2fbe1-1257">Descrizione</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2fbe1-1258">Funzione che restituisce una stringa fornita come token di autenticazione di un portatore nelle richieste HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2fbe1-1259">Impostare questa impostazione su `true` per ignorare il passaggio di negoziazione.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2fbe1-1260">**Supportato solo quando il trasporto WebSocket è l'unico trasporto abilitato**.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2fbe1-1261">Questa impostazione non può essere abilitata quando si usa il servizio di Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2fbe1-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2fbe1-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1263">Empty</span></span> | <span data-ttu-id="2fbe1-1264">Mappa di intestazioni HTTP aggiuntive da inviare con ogni richiesta HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2fbe1-1265">Nel client .NET queste opzioni possono essere modificate dal delegato options fornito a `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2fbe1-1266">Nel client JavaScript queste opzioni possono essere fornite in un oggetto JavaScript fornito a `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2fbe1-1267">Nel client Java queste opzioni possono essere configurate con i metodi sull'oggetto `HttpHubConnectionBuilder` restituito dal `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2fbe1-1268">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="2fbe1-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
