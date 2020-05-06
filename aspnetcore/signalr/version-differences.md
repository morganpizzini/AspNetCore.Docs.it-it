---
title: Differenze tra SignalR e ASP.NET CoreSignalR
author: bradygaster
description: Differenze tra SignalR e ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772579"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="3a70e-103">Differenze tra ASP.NET SignalR e ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="3a70e-104">ASP.NET Core SignalR non è compatibile con i client o i server per ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="3a70e-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="3a70e-105">Questo articolo descrive in dettaglio le funzionalità che sono state rimosse o modificate in ASP.NET Core SignalR.</span><span class="sxs-lookup"><span data-stu-id="3a70e-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="3a70e-106">Come identificare la versione di SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="3a70e-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-107">ASP.NET SignalR</span></span> | <span data-ttu-id="3a70e-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="3a70e-109">Pacchetto NuGet server</span><span class="sxs-lookup"><span data-stu-id="3a70e-109">Server NuGet package</span></span> | [<span data-ttu-id="3a70e-110">Microsoft. AspNet. SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="3a70e-111">No.</span><span class="sxs-lookup"><span data-stu-id="3a70e-111">None.</span></span> <span data-ttu-id="3a70e-112">Incluso nel Framework condiviso [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="3a70e-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="3a70e-113">Pacchetti NuGet client</span><span class="sxs-lookup"><span data-stu-id="3a70e-113">Client NuGet packages</span></span> | [<span data-ttu-id="3a70e-114">Microsoft. AspNet. SignalR. client</span><span class="sxs-lookup"><span data-stu-id="3a70e-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="3a70e-115">Microsoft. AspNet. SignalR. JS</span><span class="sxs-lookup"><span data-stu-id="3a70e-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="3a70e-116">Microsoft. AspNetCore. SignalR. client</span><span class="sxs-lookup"><span data-stu-id="3a70e-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="3a70e-117">Pacchetto NPM client JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a70e-117">JavaScript client npm package</span></span> | [<span data-ttu-id="3a70e-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="3a70e-119">Client Java</span><span class="sxs-lookup"><span data-stu-id="3a70e-119">Java client</span></span> | <span data-ttu-id="3a70e-120">[Repository GitHub](https://github.com/SignalR/java-client) (deprecato)</span><span class="sxs-lookup"><span data-stu-id="3a70e-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="3a70e-121">Pacchetto Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="3a70e-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="3a70e-122">Tipo di app Server</span><span class="sxs-lookup"><span data-stu-id="3a70e-122">Server app type</span></span> | <span data-ttu-id="3a70e-123">ASP.NET (System. Web) o OWIN Self-host</span><span class="sxs-lookup"><span data-stu-id="3a70e-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="3a70e-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a70e-124">ASP.NET Core</span></span> |
| <span data-ttu-id="3a70e-125">Piattaforme server supportate</span><span class="sxs-lookup"><span data-stu-id="3a70e-125">Supported server platforms</span></span> | <span data-ttu-id="3a70e-126">.NET Framework 4,5 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="3a70e-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="3a70e-127">.NET Core 3,0 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="3a70e-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="3a70e-128">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-128">ASP.NET SignalR</span></span> | <span data-ttu-id="3a70e-129">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="3a70e-130">Pacchetto NuGet server</span><span class="sxs-lookup"><span data-stu-id="3a70e-130">Server NuGet package</span></span> | <span data-ttu-id="3a70e-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="3a70e-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="3a70e-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="3a70e-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="3a70e-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="3a70e-134">(.NET Framework)</span></span> |
| <span data-ttu-id="3a70e-135">Pacchetti NuGet client</span><span class="sxs-lookup"><span data-stu-id="3a70e-135">Client NuGet packages</span></span> | <span data-ttu-id="3a70e-136">[Microsoft. AspNet. SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="3a70e-137">[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="3a70e-138">[Microsoft. AspNetCore. SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="3a70e-139">Pacchetto NPM client JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a70e-139">JavaScript client npm package</span></span> | [<span data-ttu-id="3a70e-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="3a70e-141">Client Java</span><span class="sxs-lookup"><span data-stu-id="3a70e-141">Java client</span></span> | <span data-ttu-id="3a70e-142">[Repository GitHub](https://github.com/SignalR/java-client) (deprecato)</span><span class="sxs-lookup"><span data-stu-id="3a70e-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="3a70e-143">Pacchetto Maven [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="3a70e-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="3a70e-144">Tipo di app Server</span><span class="sxs-lookup"><span data-stu-id="3a70e-144">Server app type</span></span> | <span data-ttu-id="3a70e-145">ASP.NET (System. Web) o OWIN Self-host</span><span class="sxs-lookup"><span data-stu-id="3a70e-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="3a70e-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a70e-146">ASP.NET Core</span></span> |
| <span data-ttu-id="3a70e-147">Piattaforme server supportate</span><span class="sxs-lookup"><span data-stu-id="3a70e-147">Supported server platforms</span></span> | <span data-ttu-id="3a70e-148">.NET Framework 4,5 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="3a70e-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="3a70e-149">.NET Framework 4.6.1 o versioni successive</span><span class="sxs-lookup"><span data-stu-id="3a70e-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="3a70e-150">.NET Core 2,1 o versione successiva</span><span class="sxs-lookup"><span data-stu-id="3a70e-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="3a70e-151">Differenze tra le funzionalità</span><span class="sxs-lookup"><span data-stu-id="3a70e-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="3a70e-152">Riconnessioni automatiche</span><span class="sxs-lookup"><span data-stu-id="3a70e-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a70e-153">In ASP.NET SignalR:</span><span class="sxs-lookup"><span data-stu-id="3a70e-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="3a70e-154">Per impostazione predefinita SignalR , tenta di riconnettersi al server se la connessione viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="3a70e-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="3a70e-155">In ASP.NET Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="3a70e-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="3a70e-156">Le riconnessioni automatiche sono esplicite per il client [.NET](xref:signalr/dotnet-client#automatically-reconnect) e il [client JavaScript](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="3a70e-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a70e-157">Prima di ASP.NET Core 3,0, SignalR non supporta la riconnessione automatica.</span><span class="sxs-lookup"><span data-stu-id="3a70e-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="3a70e-158">Se il client è disconnesso, l'utente deve avviare in modo esplicito una nuova connessione per riconnettersi.</span><span class="sxs-lookup"><span data-stu-id="3a70e-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="3a70e-159">In ASP.NET SignalR SignalR tenta di riconnettersi al server se la connessione viene eliminata.</span><span class="sxs-lookup"><span data-stu-id="3a70e-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="3a70e-160">Supporto dei protocolli</span><span class="sxs-lookup"><span data-stu-id="3a70e-160">Protocol support</span></span>

<span data-ttu-id="3a70e-161">ASP.NET Core SignalR supporta JSON, nonché un nuovo protocollo binario basato su [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="3a70e-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="3a70e-162">Inoltre, è possibile creare protocolli personalizzati.</span><span class="sxs-lookup"><span data-stu-id="3a70e-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="3a70e-163">Trasporti</span><span class="sxs-lookup"><span data-stu-id="3a70e-163">Transports</span></span>

<span data-ttu-id="3a70e-164">Il trasporto con frame Forever non è supportato SignalRin ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a70e-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="3a70e-165">Differenze sul server</span><span class="sxs-lookup"><span data-stu-id="3a70e-165">Differences on the server</span></span>

<span data-ttu-id="3a70e-166">Le librerie SignalR del lato server ASP.NET Core sono incluse in [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), che viene usato nel modello di **applicazione Web ASP.NET Core** per i Razor progetti MVC e.</span><span class="sxs-lookup"><span data-stu-id="3a70e-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="3a70e-167">ASP.NET Core SignalR è un middleware di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a70e-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="3a70e-168">Deve essere configurata chiamando <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3a70e-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a70e-169">Per configurare il routing, mappare le route agli <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Hub all'interno della `Startup.Configure` chiamata al metodo nel metodo.</span><span class="sxs-lookup"><span data-stu-id="3a70e-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a70e-170">Per configurare il routing, mappare le route agli <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> Hub all'interno della `Startup.Configure` chiamata al metodo nel metodo.</span><span class="sxs-lookup"><span data-stu-id="3a70e-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="3a70e-171">Sessioni permanenti</span><span class="sxs-lookup"><span data-stu-id="3a70e-171">Sticky sessions</span></span>

<span data-ttu-id="3a70e-172">Il modello di scalabilità orizzontale SignalR per ASP.NET consente ai client di riconnettersi e inviare messaggi a qualsiasi server della farm.</span><span class="sxs-lookup"><span data-stu-id="3a70e-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="3a70e-173">In ASP.NET Core SignalR, il client deve interagire con lo stesso server per la durata della connessione.</span><span class="sxs-lookup"><span data-stu-id="3a70e-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="3a70e-174">Per la scalabilità orizzontale con Redis, significa che sono necessarie sessioni permanenti.</span><span class="sxs-lookup"><span data-stu-id="3a70e-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="3a70e-175">Per la scalabilità orizzontale tramite il [servizio Azure SignalR ](/azure/azure-signalr/), le sessioni permanenti non sono necessarie perché il servizio gestisce le connessioni ai client.</span><span class="sxs-lookup"><span data-stu-id="3a70e-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="3a70e-176">Hub singolo per connessione</span><span class="sxs-lookup"><span data-stu-id="3a70e-176">Single hub per connection</span></span>

<span data-ttu-id="3a70e-177">In ASP.NET Core SignalR, il modello di connessione è stato semplificato.</span><span class="sxs-lookup"><span data-stu-id="3a70e-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="3a70e-178">Le connessioni vengono effettuate direttamente in un singolo hub, anziché una singola connessione usata per condividere l'accesso a più hub.</span><span class="sxs-lookup"><span data-stu-id="3a70e-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="3a70e-179">Streaming</span><span class="sxs-lookup"><span data-stu-id="3a70e-179">Streaming</span></span>

<span data-ttu-id="3a70e-180">ASP.NET Core SignalR supporta ora lo [streaming dei dati](xref:signalr/streaming) dall'hub al client.</span><span class="sxs-lookup"><span data-stu-id="3a70e-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="3a70e-181">State</span><span class="sxs-lookup"><span data-stu-id="3a70e-181">State</span></span>

<span data-ttu-id="3a70e-182">La possibilità di passare uno stato arbitrario tra i client e l'hub ( `HubState`spesso chiamato) è stata rimossa, oltre al supporto per i messaggi di stato.</span><span class="sxs-lookup"><span data-stu-id="3a70e-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="3a70e-183">Al momento non è presente alcuna controparte dei proxy dell'hub.</span><span class="sxs-lookup"><span data-stu-id="3a70e-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="3a70e-184">Rimozione di PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="3a70e-184">PersistentConnection removal</span></span>

<span data-ttu-id="3a70e-185">In ASP.NET Core SignalR, la classe [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) è stata rimossa.</span><span class="sxs-lookup"><span data-stu-id="3a70e-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="3a70e-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="3a70e-186">GlobalHost</span></span>

<span data-ttu-id="3a70e-187">ASP.NET Core dispone DI INSERIMENTO DI dipendenze (DI) incorporato nel Framework.</span><span class="sxs-lookup"><span data-stu-id="3a70e-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="3a70e-188">I servizi possono usare l'inserimento DI dipendenze per accedere a [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="3a70e-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="3a70e-189">L' `GlobalHost` oggetto usato in ASP.NET SignalR per ottenere un `HubContext` oggetto non esiste nel ASP.NET Core. SignalR</span><span class="sxs-lookup"><span data-stu-id="3a70e-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="3a70e-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="3a70e-190">HubPipeline</span></span>

<span data-ttu-id="3a70e-191">ASP.NET Core SignalR non dispone del supporto `HubPipeline` per i moduli.</span><span class="sxs-lookup"><span data-stu-id="3a70e-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="3a70e-192">Differenze sul client</span><span class="sxs-lookup"><span data-stu-id="3a70e-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="3a70e-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="3a70e-193">TypeScript</span></span>

<span data-ttu-id="3a70e-194">Il client SignalR ASP.NET Core è scritto in [typescript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="3a70e-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="3a70e-195">È possibile scrivere in JavaScript o TypeScript quando si usa il [client JavaScript](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="3a70e-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="3a70e-196">Il client JavaScript è ospitato in NPM</span><span class="sxs-lookup"><span data-stu-id="3a70e-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a70e-197">Nelle versioni ASP.NET il client JavaScript è stato ottenuto tramite un pacchetto NuGet in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a70e-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="3a70e-198">Nelle versioni ASP.NET Core il [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pacchetto NPM contiene le librerie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a70e-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="3a70e-199">Questo pacchetto non è incluso nel modello di **applicazione Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="3a70e-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="3a70e-200">Usare NPM per ottenere e installare il `@microsoft/signalr` pacchetto NPM.</span><span class="sxs-lookup"><span data-stu-id="3a70e-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a70e-201">Nelle versioni ASP.NET il client JavaScript è stato ottenuto tramite un pacchetto NuGet in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3a70e-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="3a70e-202">Nelle versioni ASP.NET Core il [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pacchetto NPM contiene le librerie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a70e-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="3a70e-203">Questo pacchetto non è incluso nel modello di **applicazione Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="3a70e-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="3a70e-204">Usare NPM per ottenere e installare il `@aspnet/signalr` pacchetto NPM.</span><span class="sxs-lookup"><span data-stu-id="3a70e-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="3a70e-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="3a70e-205">jQuery</span></span>

<span data-ttu-id="3a70e-206">La dipendenza da jQuery è stata rimossa, tuttavia i progetti possono comunque usare jQuery.</span><span class="sxs-lookup"><span data-stu-id="3a70e-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="3a70e-207">Supporto di Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="3a70e-207">Internet Explorer support</span></span>

<span data-ttu-id="3a70e-208">ASP.NET Core SignalR richiede Microsoft Internet Explorer 11 o versione successiva ( SignalR ASP.NET supporta Microsoft Internet Explorer 8 e versioni successive).</span><span class="sxs-lookup"><span data-stu-id="3a70e-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="3a70e-209">Sintassi del metodo client JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a70e-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a70e-210">La sintassi JavaScript è stata modificata rispetto alla versione ASP.NET SignalRdi.</span><span class="sxs-lookup"><span data-stu-id="3a70e-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="3a70e-211">Anziché usare l' `$connection` oggetto, creare una connessione usando l'API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="3a70e-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="3a70e-212">Usare il metodo [on](/javascript/api/@microsoft/signalr/HubConnection#on) per specificare i metodi client che l'hub può chiamare.</span><span class="sxs-lookup"><span data-stu-id="3a70e-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a70e-213">La sintassi JavaScript è stata modificata rispetto alla versione ASP.NET SignalRdi.</span><span class="sxs-lookup"><span data-stu-id="3a70e-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="3a70e-214">Anziché usare l' `$connection` oggetto, creare una connessione usando l'API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .</span><span class="sxs-lookup"><span data-stu-id="3a70e-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="3a70e-215">Usare il metodo [on](/javascript/api/@aspnet/signalr/HubConnection#on) per specificare i metodi client che l'hub può chiamare.</span><span class="sxs-lookup"><span data-stu-id="3a70e-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="3a70e-216">Dopo aver creato il metodo client, avviare la connessione dell'hub.</span><span class="sxs-lookup"><span data-stu-id="3a70e-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="3a70e-217">Concatenare un metodo [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) per registrare o gestire gli errori.</span><span class="sxs-lookup"><span data-stu-id="3a70e-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="3a70e-218">Proxy Hub</span><span class="sxs-lookup"><span data-stu-id="3a70e-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a70e-219">I proxy Hub non vengono più generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3a70e-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="3a70e-220">Al contrario, il nome del metodo viene passato nell'API [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) sotto forma di stringa.</span><span class="sxs-lookup"><span data-stu-id="3a70e-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="3a70e-221">I proxy Hub non vengono più generati automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3a70e-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="3a70e-222">Al contrario, il nome del metodo viene passato nell'API [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) sotto forma di stringa.</span><span class="sxs-lookup"><span data-stu-id="3a70e-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="3a70e-223">.NET e altri client</span><span class="sxs-lookup"><span data-stu-id="3a70e-223">.NET and other clients</span></span>

<span data-ttu-id="3a70e-224">[Microsoft. AspNetCore.SignalR ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Il pacchetto NuGet client contiene le librerie client .NET per SignalRASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a70e-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="3a70e-225">Usare <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> per creare e compilare un'istanza di una connessione a un hub.</span><span class="sxs-lookup"><span data-stu-id="3a70e-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="3a70e-226">Differenze di scalabilità orizzontale</span><span class="sxs-lookup"><span data-stu-id="3a70e-226">Scaleout differences</span></span>

<span data-ttu-id="3a70e-227">ASP.NET SignalR supporta SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="3a70e-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="3a70e-228">ASP.NET Core SignalR supporta il SignalR servizio di Azure e Redis.</span><span class="sxs-lookup"><span data-stu-id="3a70e-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="3a70e-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3a70e-229">ASP.NET</span></span>

* <span data-ttu-id="3a70e-230">[SignalRscalabilità orizzontale con il bus di servizio di Azure](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="3a70e-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="3a70e-231">[SignalRscalabilità orizzontale con Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="3a70e-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="3a70e-232">[SignalRscalabilità orizzontale con SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="3a70e-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="3a70e-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a70e-233">ASP.NET Core</span></span>

* <span data-ttu-id="3a70e-234">[Servizio SignalR di Azure](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="3a70e-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="3a70e-235">Backplane Redis</span><span class="sxs-lookup"><span data-stu-id="3a70e-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="3a70e-236">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="3a70e-236">Additional resources</span></span>

* [<span data-ttu-id="3a70e-237">Hub</span><span class="sxs-lookup"><span data-stu-id="3a70e-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="3a70e-238">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a70e-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="3a70e-239">Client .NET</span><span class="sxs-lookup"><span data-stu-id="3a70e-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="3a70e-240">Piattaforme supportate</span><span class="sxs-lookup"><span data-stu-id="3a70e-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
