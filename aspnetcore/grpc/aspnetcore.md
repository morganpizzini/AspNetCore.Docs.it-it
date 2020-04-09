---
title: Servizi gRPC con ASP.NET Core
author: juntaoluo
description: Scopri i concetti di base quando scrivi servizi gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667629"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="0a709-103">Servizi gRPC con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a709-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="0a709-104">Questo documento mostra come iniziare a utilizzare i servizi gRPC usando ASP.NET Core.This document shows how to get started with gRPC services using ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a709-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a709-105">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="0a709-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a709-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a709-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a709-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a709-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a709-108">Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0a709-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="0a709-109">Introduzione all'uso del servizio gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a709-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="0a709-110">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0a709-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a709-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a709-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a709-112">Vedere [Introduzione ai servizi gRPC](xref:tutorials/grpc/grpc-start) per istruzioni dettagliate su come creare un progetto gRPC.</span><span class="sxs-lookup"><span data-stu-id="0a709-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="0a709-113">Visual Studio Code / Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="0a709-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0a709-114">Eseguire `dotnet new grpc -o GrpcGreeter` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="0a709-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="0a709-115">Aggiungere servizi gRPC a un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a709-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="0a709-116">gRPC richiede il pacchetto [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="0a709-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="0a709-117">Configurare gRPC</span><span class="sxs-lookup"><span data-stu-id="0a709-117">Configure gRPC</span></span>

<span data-ttu-id="0a709-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0a709-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="0a709-119">gRPC è abilitato `AddGrpc` con il metodo .</span><span class="sxs-lookup"><span data-stu-id="0a709-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="0a709-120">Ogni servizio gRPC viene aggiunto alla `MapGrpcService` pipeline di routing tramite il metodo.</span><span class="sxs-lookup"><span data-stu-id="0a709-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="0a709-121">ASP.NET core di base e le funzionalità condividono la pipeline di routing, pertanto un'app può essere configurata per servire gestori di richieste aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="0a709-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="0a709-122">I gestori richieste aggiuntivi, ad esempio i controller MVC, funzionano in parallelo con i servizi gRPC configurati.</span><span class="sxs-lookup"><span data-stu-id="0a709-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="0a709-123">Configurare Kestrel</span><span class="sxs-lookup"><span data-stu-id="0a709-123">Configure Kestrel</span></span>

<span data-ttu-id="0a709-124">Endpoint Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="0a709-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="0a709-125">Richiedi HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0a709-125">Require HTTP/2.</span></span>
* <span data-ttu-id="0a709-126">Deve essere protetto con [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="0a709-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="0a709-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="0a709-127">HTTP/2</span></span>

<span data-ttu-id="0a709-128">gRPC richiede HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0a709-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="0a709-129">gRPC per ASP.NET Core convalida [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="0a709-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="0a709-130">Kestrel [supporta HTTP/2](xref:fundamentals/servers/kestrel#http2-support) nella maggior parte dei sistemi operativi moderni.</span><span class="sxs-lookup"><span data-stu-id="0a709-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="0a709-131">Gli endpoint Kestrel sono configurati per supportare le connessioni HTTP/1.1 e HTTP/2 per impostazione predefinita.</span><span class="sxs-lookup"><span data-stu-id="0a709-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="0a709-132">TLS</span><span class="sxs-lookup"><span data-stu-id="0a709-132">TLS</span></span>

<span data-ttu-id="0a709-133">Gli endpoint Kestrel utilizzati per gRPC devono essere protetti con TLS.</span><span class="sxs-lookup"><span data-stu-id="0a709-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="0a709-134">In fase di sviluppo, un endpoint `https://localhost:5001` protetto con TLS viene creato automaticamente quando è presente il certificato di sviluppo ASP.NET Core.In development, an endpoint secured with TLS is automatically created at when the ASP.NET Core development certificate is present.</span><span class="sxs-lookup"><span data-stu-id="0a709-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="0a709-135">Non è richiesta alcuna configurazione.</span><span class="sxs-lookup"><span data-stu-id="0a709-135">No configuration is required.</span></span> <span data-ttu-id="0a709-136">Un `https` prefisso verifica che l'endpoint Kestrel utilizzi TLS.</span><span class="sxs-lookup"><span data-stu-id="0a709-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="0a709-137">Nell'ambiente di produzione, TLS deve essere configurato in modo esplicito.</span><span class="sxs-lookup"><span data-stu-id="0a709-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="0a709-138">Nell'esempio appsettings.json seguente viene fornito un endpoint HTTP/2 protetto con TLS:In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span><span class="sxs-lookup"><span data-stu-id="0a709-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="0a709-139">In alternativa, gli endpoint Kestrel possono essere configurati in *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="0a709-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="0a709-140">Negoziazione del protocollo</span><span class="sxs-lookup"><span data-stu-id="0a709-140">Protocol negotiation</span></span>

<span data-ttu-id="0a709-141">TLS viene utilizzato per più che proteggere le comunicazioni.</span><span class="sxs-lookup"><span data-stu-id="0a709-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="0a709-142">L'handshake [TLS Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) viene utilizzato per negoziare il protocollo di connessione tra il client e il server quando un endpoint supporta più protocolli.</span><span class="sxs-lookup"><span data-stu-id="0a709-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="0a709-143">Questa negoziazione determina se la connessione utilizza HTTP/1.1 o HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="0a709-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="0a709-144">Se un endpoint HTTP/2 è configurato senza TLS, [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) dell'endpoint deve essere impostato su `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="0a709-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="0a709-145">Un endpoint con più protocolli `HttpProtocols.Http1AndHttp2`(ad esempio, ) non può essere utilizzato senza TLS perché non esiste alcuna negoziazione.</span><span class="sxs-lookup"><span data-stu-id="0a709-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="0a709-146">Tutte le connessioni all'endpoint non protetto vengono predefinite per HTTP/1.1 e le chiamate gRPC hanno esito negativo.</span><span class="sxs-lookup"><span data-stu-id="0a709-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="0a709-147">Per ulteriori informazioni sull'abilitazione di HTTP/2 e TLS con Kestrel, vedere [Configurazione dell'endpoint Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="0a709-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="0a709-148">macOS non supporta ASP.NET Core gRPC con TLS.</span><span class="sxs-lookup"><span data-stu-id="0a709-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="0a709-149">Per eseguire correttamente i servizi gRPC in macOS, è necessaria una configurazione aggiuntiva.</span><span class="sxs-lookup"><span data-stu-id="0a709-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="0a709-150">Per altre informazioni, vedere [Non è possibile avviare un'app ASP.NET Core gRPC in macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="0a709-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="0a709-151">Integrazione con le API di base di ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0a709-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="0a709-152">I servizi gRPC hanno accesso completo alle funzionalità di ASP.NET Core, ad esempio [Inserimento](xref:fundamentals/dependency-injection) delle dipendenze (DI) e [Registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="0a709-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0a709-153">Ad esempio, l'implementazione del servizio può risolvere un servizio di logger dal contenitore DI tramite il costruttore:For example, the service implementation can resolve a logger service from the DI container via the constructor:</span><span class="sxs-lookup"><span data-stu-id="0a709-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="0a709-154">Per impostazione predefinita, l'implementazione del servizio gRPC può risolvere altri servizi DI con qualsiasi durata (Singleton, Ambito o Transitorio).</span><span class="sxs-lookup"><span data-stu-id="0a709-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="0a709-155">Risolvere HttpContext nei metodi gRPCResolve HttpContext in gRPC methods</span><span class="sxs-lookup"><span data-stu-id="0a709-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="0a709-156">L'API gRPC fornisce l'accesso ad alcuni dati del messaggio HTTP/2, ad esempio il metodo, l'host, l'intestazione e i trailer.</span><span class="sxs-lookup"><span data-stu-id="0a709-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="0a709-157">L'accesso `ServerCallContext` è tramite l'argomento passato a ogni metodo gRPC:</span><span class="sxs-lookup"><span data-stu-id="0a709-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="0a709-158">`ServerCallContext`non fornisce l'accesso completo a `HttpContext` tutte le API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0a709-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="0a709-159">Il `GetHttpContext` metodo di estensione `HttpContext` fornisce l'accesso completo al messaggio HTTP/2 sottostante nelle API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="0a709-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="0a709-160">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="0a709-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
