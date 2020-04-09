---
title: Migrazione di servizi gRPC da C-core ad ASP.NET Core
author: juntaoluo
description: Scopri come spostare un'app gRPC basata su C-core esistente per l'esecuzione ASP.NET Stack Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664136"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="36dbf-103">Migrazione di servizi gRPC da C-core ad ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36dbf-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="36dbf-104">A cura di [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="36dbf-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="36dbf-105">A causa dell'implementazione dello stack sottostante, non tutte le funzionalità funzionano allo stesso modo tra le app [gRPC basate su C-core](https://grpc.io/blog/grpc-stacks) e ASP.NET app basate su Core.</span><span class="sxs-lookup"><span data-stu-id="36dbf-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="36dbf-106">Questo documento evidenzia le differenze principali per la migrazione tra i due stack.</span><span class="sxs-lookup"><span data-stu-id="36dbf-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="36dbf-107">Durata dell'implementazione del servizio gRPC</span><span class="sxs-lookup"><span data-stu-id="36dbf-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="36dbf-108">Nello stack ASP.NET Core, i servizi gRPC, per impostazione predefinita, vengono creati con una [durata con ambito.](xref:fundamentals/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="36dbf-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="36dbf-109">Al contrario, gRPC C-core per impostazione predefinita esegue il binding a un servizio con una [durata singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="36dbf-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="36dbf-110">Una durata con ambito consente all'implementazione del servizio di risolvere altri servizi con durata con ambito.</span><span class="sxs-lookup"><span data-stu-id="36dbf-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="36dbf-111">Ad esempio, una durata con `DbContext` ambito può essere risolta anche dal contenitore DI tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="36dbf-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="36dbf-112">Utilizzo della durata con ambito:Using scoped lifetime:</span><span class="sxs-lookup"><span data-stu-id="36dbf-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="36dbf-113">Viene costruita una nuova istanza dell'implementazione del servizio per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="36dbf-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="36dbf-114">Non è possibile condividere lo stato tra le richieste tramite membri di istanza nel tipo di implementazione.</span><span class="sxs-lookup"><span data-stu-id="36dbf-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="36dbf-115">L'aspettativa consiste nell'archiviare gli stati condivisi in un servizio singleton nel contenitore DI.</span><span class="sxs-lookup"><span data-stu-id="36dbf-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="36dbf-116">Gli stati condivisi archiviati vengono risolti nel costruttore dell'implementazione del servizio gRPC.</span><span class="sxs-lookup"><span data-stu-id="36dbf-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="36dbf-117">Per ulteriori informazioni sulle durate del servizio, vedere <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="36dbf-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="36dbf-118">Aggiungere un servizio singletonAdd a singleton service</span><span class="sxs-lookup"><span data-stu-id="36dbf-118">Add a singleton service</span></span>

<span data-ttu-id="36dbf-119">Per facilitare la transizione da un'implementazione c-core gRPC a ASP.NET Core, è possibile modificare la durata del servizio dell'implementazione del servizio da con ambito a singleton.</span><span class="sxs-lookup"><span data-stu-id="36dbf-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="36dbf-120">Ciò comporta l'aggiunta di un'istanza dell'implementazione del servizio al contenitore DI:</span><span class="sxs-lookup"><span data-stu-id="36dbf-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="36dbf-121">Tuttavia, un'implementazione del servizio con una durata singleton non è più in grado di risolvere i servizi con ambito tramite l'inserimento del costruttore.</span><span class="sxs-lookup"><span data-stu-id="36dbf-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="36dbf-122">Configurare le opzioni dei servizi gRPC</span><span class="sxs-lookup"><span data-stu-id="36dbf-122">Configure gRPC services options</span></span>

<span data-ttu-id="36dbf-123">Nelle app basate su C-core, `grpc.max_send_message_length` le `ChannelOption` impostazioni come `grpc.max_receive_message_length` e vengono configurate con [durante la costruzione dell'istanza Server](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="36dbf-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="36dbf-124">In ASP.NET Core, gRPC `GrpcServiceOptions` fornisce la configurazione tramite il tipo.</span><span class="sxs-lookup"><span data-stu-id="36dbf-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="36dbf-125">Ad esempio, la dimensione massima dei messaggi in ingresso `AddGrpc`di un servizio gRPC può essere configurata tramite .</span><span class="sxs-lookup"><span data-stu-id="36dbf-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="36dbf-126">Nell'esempio seguente `MaxReceiveMessageSize` viene modificato il valore predefinito di 4 MB a 16 MB:</span><span class="sxs-lookup"><span data-stu-id="36dbf-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="36dbf-127">Per ulteriori informazioni sulla <xref:grpc/configuration>configurazione, vedere .</span><span class="sxs-lookup"><span data-stu-id="36dbf-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="36dbf-128">Registrazione</span><span class="sxs-lookup"><span data-stu-id="36dbf-128">Logging</span></span>

<span data-ttu-id="36dbf-129">Le app basate su C-core si basano su `GrpcEnvironment` di [configurare il logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) a scopo di debug.</span><span class="sxs-lookup"><span data-stu-id="36dbf-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="36dbf-130">Lo stack ASP.NET Core fornisce questa funzionalità tramite [l'API di registrazione](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="36dbf-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="36dbf-131">Ad esempio, un logger può essere aggiunto al servizio gRPC tramite inserimento del costruttore:For example, a logger can be added to the gRPC service via constructor injection:</span><span class="sxs-lookup"><span data-stu-id="36dbf-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="36dbf-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="36dbf-132">HTTPS</span></span>

<span data-ttu-id="36dbf-133">Le app basate su C-core configurano HTTPS tramite la [proprietà Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="36dbf-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="36dbf-134">Un concetto simile viene utilizzato per configurare i server in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36dbf-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="36dbf-135">Ad esempio, Kestrel utilizza la [configurazione dell'endpoint](xref:fundamentals/servers/kestrel#endpoint-configuration) per questa funzionalità.</span><span class="sxs-lookup"><span data-stu-id="36dbf-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="36dbf-136">Intercettori gRPC e middleware</span><span class="sxs-lookup"><span data-stu-id="36dbf-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="36dbf-137">ASP.NET [middleware](xref:fundamentals/middleware/index) Core offre funzionalità simili rispetto agli intercettori nelle app gRPC basate su C-core.</span><span class="sxs-lookup"><span data-stu-id="36dbf-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="36dbf-138">ASP.NET middleware Core e intercettori sono concettualmente simili.</span><span class="sxs-lookup"><span data-stu-id="36dbf-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="36dbf-139">Both:</span><span class="sxs-lookup"><span data-stu-id="36dbf-139">Both:</span></span>

* <span data-ttu-id="36dbf-140">Vengono utilizzati per costruire una pipeline che gestisce una richiesta gRPC.</span><span class="sxs-lookup"><span data-stu-id="36dbf-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="36dbf-141">Consentire l'esecuzione del lavoro prima o dopo il componente successivo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="36dbf-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="36dbf-142">Fornire l'accesso a `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="36dbf-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="36dbf-143">Nel middleware `HttpContext` è un parametro.</span><span class="sxs-lookup"><span data-stu-id="36dbf-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="36dbf-144">Negli intercettori `HttpContext` è possibile `ServerCallContext` accedere `ServerCallContext.GetHttpContext` al parametro con il metodo di estensione.</span><span class="sxs-lookup"><span data-stu-id="36dbf-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="36dbf-145">Si noti che questa funzionalità è specifica per gli intercettori in esecuzione in ASP.NET Core.Note that this feature is specific to interceptors running in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36dbf-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="36dbf-146">gRPC Interceptor differenze da ASP.NET Core Middleware:</span><span class="sxs-lookup"><span data-stu-id="36dbf-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="36dbf-147">Intercettori:</span><span class="sxs-lookup"><span data-stu-id="36dbf-147">Interceptors:</span></span>
  * <span data-ttu-id="36dbf-148">Operare sul livello gRPC dell'astrazione utilizzando [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="36dbf-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="36dbf-149">Fornire l'accesso a:</span><span class="sxs-lookup"><span data-stu-id="36dbf-149">Provide access to:</span></span>
    * <span data-ttu-id="36dbf-150">Messaggio deserializzato inviato a una chiamata.</span><span class="sxs-lookup"><span data-stu-id="36dbf-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="36dbf-151">Messaggio restituito dalla chiamata prima che venga serializzato.</span><span class="sxs-lookup"><span data-stu-id="36dbf-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="36dbf-152">Può intercettare e gestire le eccezioni generate dai servizi gRPC.</span><span class="sxs-lookup"><span data-stu-id="36dbf-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="36dbf-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="36dbf-153">Middleware:</span></span>
  * <span data-ttu-id="36dbf-154">Viene eseguito prima degli intercettori gRPC.</span><span class="sxs-lookup"><span data-stu-id="36dbf-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="36dbf-155">Opera sui messaggi HTTP/2 sottostanti.</span><span class="sxs-lookup"><span data-stu-id="36dbf-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="36dbf-156">Può accedere solo ai byte dei flussi di richiesta e risposta.</span><span class="sxs-lookup"><span data-stu-id="36dbf-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36dbf-157">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="36dbf-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
