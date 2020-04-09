---
title: Integrazione di factory client gRPC in .NET Core
author: jamesnk
description: Informazioni su come creare client gRPC utilizzando la factory client.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667167"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="fc408-103">Integrazione di factory client gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="fc408-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="fc408-104">l'integrazione `HttpClientFactory` gRPC con offre un modo centralizzato per creare client gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc408-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="fc408-105">Può essere utilizzato come alternativa alla configurazione di [istanze client gRPC autonome.](xref:grpc/client)</span><span class="sxs-lookup"><span data-stu-id="fc408-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="fc408-106">L'integrazione di fabbrica è disponibile nel pacchetto [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="fc408-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="fc408-107">La fabbrica offre i seguenti vantaggi:</span><span class="sxs-lookup"><span data-stu-id="fc408-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="fc408-108">Fornisce una posizione centrale per la configurazione di istanze logiche del client gRPC</span><span class="sxs-lookup"><span data-stu-id="fc408-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="fc408-109">Gestisce la durata del`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="fc408-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="fc408-110">Propagazione automatica della scadenza e dell'annullamento in un servizio gRPC ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc408-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="fc408-111">Registrare i client gRPC</span><span class="sxs-lookup"><span data-stu-id="fc408-111">Register gRPC clients</span></span>

<span data-ttu-id="fc408-112">Per registrare un client `AddGrpcClient` gRPC, è `Startup.ConfigureServices`possibile utilizzare il metodo di estensione generico all'interno di , specificando la classe client tipizzata gRPC e l'indirizzo del servizio:</span><span class="sxs-lookup"><span data-stu-id="fc408-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="fc408-113">Il tipo di client gRPC viene registrato come temporaneo con inserimento di dipendenze (DI).</span><span class="sxs-lookup"><span data-stu-id="fc408-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="fc408-114">Il client può ora essere iniettato e utilizzato direttamente nei tipi creati da DI.</span><span class="sxs-lookup"><span data-stu-id="fc408-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="fc408-115">ASP.NET controller MVC SignalR principali, hub e servizi gRPC sono luoghi in cui i client gRPC possono essere inseriti automaticamente:</span><span class="sxs-lookup"><span data-stu-id="fc408-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="fc408-116">Configurare HttpClient</span><span class="sxs-lookup"><span data-stu-id="fc408-116">Configure HttpClient</span></span>

<span data-ttu-id="fc408-117">`HttpClientFactory`crea `HttpClient` l'oggetto utilizzato dal client gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc408-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="fc408-118">I `HttpClientFactory` metodi standard possono essere utilizzati per aggiungere `HttpClientHandler` middleware per richieste in uscita o per configurare il sottostante del: `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="fc408-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="fc408-119">Per ulteriori informazioni, vedere [Effettuare richieste HTTP utilizzando IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="fc408-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="fc408-120">Configurare il canale e gli intercettori</span><span class="sxs-lookup"><span data-stu-id="fc408-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="fc408-121">Sono disponibili metodi specifici gRPC per:</span><span class="sxs-lookup"><span data-stu-id="fc408-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="fc408-122">Configurare il canale sottostante di un client gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc408-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="fc408-123">Aggiungere `Interceptor` istanze che verranno utilizzate dal client durante l'esecuzione di chiamate gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc408-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="fc408-124">Scadenza e propagazione dell'annullamento</span><span class="sxs-lookup"><span data-stu-id="fc408-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="fc408-125">I client gRPC creati dalla factory in un `EnableCallContextPropagation()` servizio gRPC possono essere configurati con per propagare automaticamente la scadenza e il token di annullamento per le chiamate figlio.</span><span class="sxs-lookup"><span data-stu-id="fc408-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="fc408-126">Il `EnableCallContextPropagation()` metodo di estensione è disponibile nel pacchetto [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="fc408-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="fc408-127">La propagazione del contesto delle chiamate funziona leggendo la scadenza e il token di annullamento dal contesto della richiesta gRPC corrente e propagandoli automaticamente alle chiamate in uscita effettuate dal client gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc408-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="fc408-128">La propagazione del contesto delle chiamate è un ottimo modo per garantire che scenari gRPC complessi e annidati propaghino sempre la scadenza e l'annullamento.</span><span class="sxs-lookup"><span data-stu-id="fc408-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="fc408-129">Per ulteriori informazioni sulle scadenze e sull'annullamento RPC, vedere Ciclo di [vita RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="fc408-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fc408-130">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="fc408-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
