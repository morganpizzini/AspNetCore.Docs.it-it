---
title: Servizi gRPC con ASP.NET Core
author: juntaoluo
description: Informazioni sui concetti di base durante la scrittura di servizi gRPC con ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: c99a499fad824c3ac026f6f390c826c0418fc069
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59515475"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="a71ae-103">Servizi gRPC con ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a71ae-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="a71ae-104">Questo documento illustra come iniziare a usare servizi gRPC usando ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a71ae-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="a71ae-105">Introduzione all'uso del servizio gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a71ae-105">Get started with gRPC service in ASP.NET Core</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a71ae-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a71ae-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a71ae-107">Visualizzare [Introduzione a servizi gRPC](xref:tutorials/grpc/grpc-start) per istruzioni dettagliate su come creare un progetto gRPC.</span><span class="sxs-lookup"><span data-stu-id="a71ae-107">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a71ae-108">Visual Studio Code/Visual Studio per Mac</span><span class="sxs-lookup"><span data-stu-id="a71ae-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a71ae-109">Eseguire `dotnet new grpc -o GrpcGreeter` dalla riga di comando.</span><span class="sxs-lookup"><span data-stu-id="a71ae-109">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="a71ae-110">Aggiungere servizi gRPC a un'app ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a71ae-110">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="a71ae-111">gRPC richiede i pacchetti seguenti:</span><span class="sxs-lookup"><span data-stu-id="a71ae-111">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="a71ae-112">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="a71ae-112">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="a71ae-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) per protobuf API del messaggio.</span><span class="sxs-lookup"><span data-stu-id="a71ae-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="a71ae-114">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="a71ae-114">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="a71ae-115">Configurare gRPC</span><span class="sxs-lookup"><span data-stu-id="a71ae-115">Configure gRPC</span></span>

<span data-ttu-id="a71ae-116">gRPC è abilitata con la `AddGrpc` metodo:</span><span class="sxs-lookup"><span data-stu-id="a71ae-116">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="a71ae-117">Ogni servizio gRPC viene aggiunto alla pipeline di routing tramite il `MapGrpcService` metodo:</span><span class="sxs-lookup"><span data-stu-id="a71ae-117">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Startup.cs?name=snippet&highlight=21)]

<span data-ttu-id="a71ae-118">Le funzionalità e il middleware di ASP.NET Core condividono la pipeline di routing, pertanto è possibile configurare un'app per servire i gestori di richiesta aggiuntivi.</span><span class="sxs-lookup"><span data-stu-id="a71ae-118">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="a71ae-119">I gestori di richiesta aggiuntivi, ad esempio i controller MVC, operare in parallelo con i servizi configurati gRPC.</span><span class="sxs-lookup"><span data-stu-id="a71ae-119">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="a71ae-120">Integrazione con le API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a71ae-120">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="a71ae-121">gRPC servizi hanno accesso completo alle funzionalità di ASP.NET Core, ad esempio [inserimento delle dipendenze](xref:fundamentals/dependency-injection) (inserimento delle dipendenze) e [Logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a71ae-121">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a71ae-122">Ad esempio, l'implementazione del servizio può risolvere un servizio del logger dal contenitore di inserimento delle dipendenze tramite il costruttore:</span><span class="sxs-lookup"><span data-stu-id="a71ae-122">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="a71ae-123">Per impostazione predefinita, l'implementazione del servizio gRPC può risolvere altri servizi di inserimento delle dipendenze con qualsiasi ciclo di vita (Singleton, con ambito o temporaneo).</span><span class="sxs-lookup"><span data-stu-id="a71ae-123">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="a71ae-124">Risolvere HttpContext nei metodi gRPC</span><span class="sxs-lookup"><span data-stu-id="a71ae-124">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="a71ae-125">GRPC API fornisce l'accesso ad alcuni dati di messaggio HTTP/2, ad esempio il metodo, host, intestazione e trailer.</span><span class="sxs-lookup"><span data-stu-id="a71ae-125">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="a71ae-126">L'accesso avviene tramite il `ServerCallContext` argomento passato a ogni metodo gRPC:</span><span class="sxs-lookup"><span data-stu-id="a71ae-126">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="a71ae-127">`ServerCallContext` non fornisce accesso completo a `HttpContext` tutti APIs ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="a71ae-127">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="a71ae-128">Il `GetHttpContext` metodo di estensione fornisce accesso completo per il `HttpContext` che rappresenta il messaggio HTTP/2 sottostante in APIs ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="a71ae-128">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet1)]

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="a71ae-129">Limite di velocità dati di corpo della richiesta</span><span class="sxs-lookup"><span data-stu-id="a71ae-129">Request body data rate limit</span></span>

<span data-ttu-id="a71ae-130">Per impostazione predefinita, il server Kestrel impone una [velocità dei dati del corpo della richiesta minima](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span><span class="sxs-lookup"><span data-stu-id="a71ae-130">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="a71ae-131">Per client di streaming e duplex flusso delle chiamate, questa frequenza può non essere soddisfatta e la connessione potrebbe essere scaduta. Il valore minimo della richiesta corpo limite di velocità dati deve essere disabilitata quando il servizio gRPC include client di streaming e duplex flusso delle chiamate:</span><span class="sxs-lookup"><span data-stu-id="a71ae-131">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="a71ae-132">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="a71ae-132">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>