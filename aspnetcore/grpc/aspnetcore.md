---
title: Servizi gRPC con ASP.NET Core
author: juntaoluo
description: Informazioni sui concetti di base per la scrittura di servizi gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/aspnetcore
ms.openlocfilehash: fa38ec9f9cf882b1a62f74879b7d49706ee150ce
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452382"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="48760-103">Servizi gRPC con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48760-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="48760-104">Questo documento illustra come iniziare a usare i servizi di gRPC con ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48760-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="48760-105">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="48760-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48760-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48760-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="48760-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48760-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="48760-108">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="48760-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="48760-109">Introduzione all'uso del servizio gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48760-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="48760-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="48760-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48760-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48760-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="48760-112">Per istruzioni dettagliate su come creare un progetto gRPC, vedere [Introduzione ai servizi gRPC](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="48760-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="48760-113">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="48760-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="48760-114">Eseguire `dotnet new grpc -o GrpcGreeter` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="48760-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="48760-115">Aggiungere servizi gRPC a un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48760-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="48760-116">gRPC richiede il pacchetto [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="48760-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="48760-117">Configurare gRPC</span><span class="sxs-lookup"><span data-stu-id="48760-117">Configure gRPC</span></span>

<span data-ttu-id="48760-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="48760-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="48760-119">gRPC è abilitato con il `AddGrpc` metodo.</span><span class="sxs-lookup"><span data-stu-id="48760-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="48760-120">Ogni servizio gRPC viene aggiunto alla pipeline di routing tramite il `MapGrpcService` metodo.</span><span class="sxs-lookup"><span data-stu-id="48760-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="48760-121">ASP.NET Core middleware e funzionalità condividono la pipeline di routing, di conseguenza è possibile configurare un'app per gestire gestori di richieste aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="48760-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="48760-122">I gestori di richieste aggiuntivi, ad esempio i controller MVC, funzionano in parallelo con i servizi gRPC configurati.</span><span class="sxs-lookup"><span data-stu-id="48760-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="48760-123">Configurare gheppio</span><span class="sxs-lookup"><span data-stu-id="48760-123">Configure Kestrel</span></span>

<span data-ttu-id="48760-124">Endpoint gRPC di Gheppio:</span><span class="sxs-lookup"><span data-stu-id="48760-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="48760-125">Richiedi HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="48760-125">Require HTTP/2.</span></span>
* <span data-ttu-id="48760-126">Deve essere protetto con [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="48760-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="48760-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="48760-127">HTTP/2</span></span>

<span data-ttu-id="48760-128">gRPC richiede HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="48760-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="48760-129">gRPC per ASP.NET Core convalida [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) è `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="48760-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="48760-130">Gheppio [supporta http/2](xref:fundamentals/servers/kestrel#http2-support) nei sistemi operativi più recenti.</span><span class="sxs-lookup"><span data-stu-id="48760-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="48760-131">Per impostazione predefinita, gli endpoint gheppio sono configurati per supportare connessioni HTTP/1.1 e HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="48760-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="48760-132">TLS</span><span class="sxs-lookup"><span data-stu-id="48760-132">TLS</span></span>

<span data-ttu-id="48760-133">Gli endpoint gheppio usati per gRPC devono essere protetti con TLS.</span><span class="sxs-lookup"><span data-stu-id="48760-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="48760-134">In fase di sviluppo, un endpoint protetto con TLS viene creato automaticamente in corrispondenza del momento in `https://localhost:5001` cui è presente il certificato di sviluppo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="48760-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="48760-135">Non è richiesta alcuna configurazione.</span><span class="sxs-lookup"><span data-stu-id="48760-135">No configuration is required.</span></span> <span data-ttu-id="48760-136">Un `https` prefisso verifica che l'endpoint gheppio stia usando TLS.</span><span class="sxs-lookup"><span data-stu-id="48760-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="48760-137">In produzione, è necessario configurare in modo esplicito TLS.</span><span class="sxs-lookup"><span data-stu-id="48760-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="48760-138">Nell'esempio *appSettings. JSON* seguente viene fornito un endpoint HTTP/2 protetto con TLS:</span><span class="sxs-lookup"><span data-stu-id="48760-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="48760-139">In alternativa, è possibile configurare gli endpoint gheppio in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="48760-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="48760-140">Negoziazione del protocollo</span><span class="sxs-lookup"><span data-stu-id="48760-140">Protocol negotiation</span></span>

<span data-ttu-id="48760-141">TLS viene usato per una maggiore sicurezza della comunicazione.</span><span class="sxs-lookup"><span data-stu-id="48760-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="48760-142">L'handshake TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) viene utilizzato per negoziare il protocollo di connessione tra il client e il server quando un endpoint supporta più protocolli.</span><span class="sxs-lookup"><span data-stu-id="48760-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="48760-143">Questa negoziazione determina se la connessione utilizza HTTP/1.1 o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="48760-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="48760-144">Se un endpoint HTTP/2 viene configurato senza TLS, l'endpoint [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) deve essere impostato su `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="48760-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="48760-145">Un endpoint con più protocolli (ad esempio, `HttpProtocols.Http1AndHttp2` ) non può essere usato senza TLS perché non esiste alcuna negoziazione.</span><span class="sxs-lookup"><span data-stu-id="48760-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="48760-146">Per impostazione predefinita, tutte le connessioni all'endpoint non protetto sono HTTP/1.1 e le chiamate a gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="48760-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="48760-147">Per ulteriori informazioni sull'abilitazione di HTTP/2 e TLS con gheppio, vedere la pagina relativa alla [configurazione dell'endpoint gheppio](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="48760-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="48760-148">macOS non supporta ASP.NET Core gRPC con TLS.</span><span class="sxs-lookup"><span data-stu-id="48760-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="48760-149">Per eseguire correttamente i servizi gRPC in macOS, è necessaria una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="48760-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="48760-150">Per altre informazioni, vedere [Non è possibile avviare un'app ASP.NET Core gRPC in macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="48760-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="48760-151">Integrazione con API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48760-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="48760-152">i servizi gRPC hanno accesso completo alle funzionalità di ASP.NET Core come l' [inserimento delle dipendenze](xref:fundamentals/dependency-injection) e la [registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="48760-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="48760-153">Ad esempio, l'implementazione del servizio può risolvere un servizio logger dal contenitore DI inserimento delle dipendenze tramite il costruttore:</span><span class="sxs-lookup"><span data-stu-id="48760-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="48760-154">Per impostazione predefinita, l'implementazione del servizio gRPC è in grado di risolvere altri servizi DI con qualsiasi durata (singleton, ambito o temporaneo).</span><span class="sxs-lookup"><span data-stu-id="48760-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="48760-155">Risolvere HttpContext nei metodi gRPC</span><span class="sxs-lookup"><span data-stu-id="48760-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="48760-156">L'API gRPC consente di accedere ad alcuni dati del messaggio HTTP/2, ad esempio il metodo, l'host, l'intestazione e i trailer.</span><span class="sxs-lookup"><span data-stu-id="48760-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="48760-157">L'accesso avviene tramite l' `ServerCallContext` argomento passato a ogni metodo gRPC:</span><span class="sxs-lookup"><span data-stu-id="48760-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="48760-158">`ServerCallContext`non fornisce l'accesso completo a `HttpContext` in tutte le api ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="48760-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="48760-159">Il `GetHttpContext` metodo di estensione fornisce accesso completo a `HttpContext` che rappresenta il messaggio http/2 sottostante nelle API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="48760-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="48760-160">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="48760-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
