---
title: Backplane Redis per la SignalR scalabilità orizzontale di ASP.NET Core
author: bradygaster
description: Informazioni su come configurare un backplane Redis per abilitare la scalabilità orizzontale per un'app SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775349"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="c614a-103">Configurare un backplane Redis per la scalabilità orizzontale di ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c614a-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="c614a-104">Di [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster)e [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="c614a-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="c614a-105">Questo articolo illustra SignalRgli aspetti specifici della configurazione di un server [Redis](https://redis.io/) da usare per la scalabilità orizzontale SignalR di un'app ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c614a-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="c614a-106">Configurare un backplane Redis</span><span class="sxs-lookup"><span data-stu-id="c614a-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="c614a-107">Distribuire un server Redis.</span><span class="sxs-lookup"><span data-stu-id="c614a-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="c614a-108">Per l'uso in produzione, è consigliabile un backplane Redis solo quando viene eseguito nella stessa data center dell' SignalR app.</span><span class="sxs-lookup"><span data-stu-id="c614a-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="c614a-109">In caso contrario, la latenza di rete comporta un peggioramento delle prestazioni.</span><span class="sxs-lookup"><span data-stu-id="c614a-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="c614a-110">Se l' SignalR app è in esecuzione nel cloud di Azure, è consigliabile SignalR usare il servizio di Azure invece di un backplane di Redis.</span><span class="sxs-lookup"><span data-stu-id="c614a-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="c614a-111">È possibile usare il servizio cache Redis di Azure per gli ambienti di sviluppo e test.</span><span class="sxs-lookup"><span data-stu-id="c614a-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="c614a-112">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="c614a-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="c614a-113">Documentazione di Redis</span><span class="sxs-lookup"><span data-stu-id="c614a-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="c614a-114">Documentazione di Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="c614a-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="c614a-115">Nell' SignalR app installare il `Microsoft.AspNetCore.SignalR.Redis` pacchetto NuGet.</span><span class="sxs-lookup"><span data-stu-id="c614a-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="c614a-116">Nel `Startup.ConfigureServices` metodo chiamare `AddRedis` dopo: `AddSignalR`</span><span class="sxs-lookup"><span data-stu-id="c614a-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="c614a-117">Configurare le opzioni in base alle esigenze:</span><span class="sxs-lookup"><span data-stu-id="c614a-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="c614a-118">La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="c614a-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c614a-119">Le opzioni specificate `ConfigurationOptions` in eseguono l'override di quelle impostate nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c614a-120">Nell'esempio seguente viene illustrato come impostare le `ConfigurationOptions` opzioni nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c614a-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c614a-121">Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.</span><span class="sxs-lookup"><span data-stu-id="c614a-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c614a-122">Nel codice precedente, `options.Configuration` viene inizializzato con qualsiasi oggetto specificato nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="c614a-123">Nell' SignalR app installare uno dei pacchetti NuGet seguenti:</span><span class="sxs-lookup"><span data-stu-id="c614a-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="c614a-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-Dipende da StackExchange. Redis 2. X.X.</span><span class="sxs-lookup"><span data-stu-id="c614a-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="c614a-125">Questo è il pacchetto consigliato per ASP.NET Core 2,2 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c614a-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="c614a-126">`Microsoft.AspNetCore.SignalR.Redis`-Dipende da StackExchange. Redis 1. X.X.</span><span class="sxs-lookup"><span data-stu-id="c614a-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="c614a-127">Questo pacchetto non è incluso in ASP.NET Core 3,0 e versioni successive.</span><span class="sxs-lookup"><span data-stu-id="c614a-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="c614a-128">Nel `Startup.ConfigureServices` metodo chiamare <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="c614a-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="c614a-129">Quando si `Microsoft.AspNetCore.SignalR.Redis`USA, <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>chiamare.</span><span class="sxs-lookup"><span data-stu-id="c614a-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="c614a-130">Configurare le opzioni in base alle esigenze:</span><span class="sxs-lookup"><span data-stu-id="c614a-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="c614a-131">La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="c614a-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c614a-132">Le opzioni specificate `ConfigurationOptions` in eseguono l'override di quelle impostate nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c614a-133">Nell'esempio seguente viene illustrato come impostare le `ConfigurationOptions` opzioni nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c614a-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c614a-134">Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.</span><span class="sxs-lookup"><span data-stu-id="c614a-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="c614a-135">Quando si `Microsoft.AspNetCore.SignalR.Redis`USA, <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>chiamare.</span><span class="sxs-lookup"><span data-stu-id="c614a-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="c614a-136">Nel codice precedente, `options.Configuration` viene inizializzato con qualsiasi oggetto specificato nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="c614a-137">Per informazioni sulle opzioni di redis, vedere la [documentazione su Redis di stackexchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="c614a-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="c614a-138">Nell' SignalR app installare il pacchetto NuGet seguente:</span><span class="sxs-lookup"><span data-stu-id="c614a-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="c614a-139">Nel `Startup.ConfigureServices` metodo chiamare <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span><span class="sxs-lookup"><span data-stu-id="c614a-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="c614a-140">Configurare le opzioni in base alle esigenze:</span><span class="sxs-lookup"><span data-stu-id="c614a-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="c614a-141">La maggior parte delle opzioni può essere impostata nella stringa di connessione o nell'oggetto [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) .</span><span class="sxs-lookup"><span data-stu-id="c614a-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="c614a-142">Le opzioni specificate `ConfigurationOptions` in eseguono l'override di quelle impostate nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="c614a-143">Nell'esempio seguente viene illustrato come impostare le `ConfigurationOptions` opzioni nell'oggetto.</span><span class="sxs-lookup"><span data-stu-id="c614a-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="c614a-144">Questo esempio aggiunge un prefisso del canale in modo che più app possano condividere la stessa istanza di redis, come illustrato nel passaggio seguente.</span><span class="sxs-lookup"><span data-stu-id="c614a-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="c614a-145">Nel codice precedente, `options.Configuration` viene inizializzato con qualsiasi oggetto specificato nella stringa di connessione.</span><span class="sxs-lookup"><span data-stu-id="c614a-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="c614a-146">Per informazioni sulle opzioni di redis, vedere la [documentazione su Redis di stackexchange](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span><span class="sxs-lookup"><span data-stu-id="c614a-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="c614a-147">Se si usa un server Redis per più SignalR app, usare un prefisso di canale diverso per ogni SignalR app.</span><span class="sxs-lookup"><span data-stu-id="c614a-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="c614a-148">L'impostazione di un prefisso del canale SignalR isola un'app da altre che usano prefissi di canale diversi.</span><span class="sxs-lookup"><span data-stu-id="c614a-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="c614a-149">Se non si assegnano prefissi diversi, un messaggio inviato da un'app a tutti i relativi client passerà a tutti i client di tutte le app che usano il server Redis come backplane.</span><span class="sxs-lookup"><span data-stu-id="c614a-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="c614a-150">Configurare il software di bilanciamento del carico server farm per le sessioni permanenti.</span><span class="sxs-lookup"><span data-stu-id="c614a-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="c614a-151">Di seguito sono riportati alcuni esempi di documentazione su come eseguire questa operazione:</span><span class="sxs-lookup"><span data-stu-id="c614a-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="c614a-152">IIS</span><span class="sxs-lookup"><span data-stu-id="c614a-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="c614a-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="c614a-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="c614a-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="c614a-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="c614a-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="c614a-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="c614a-156">Errori del server Redis</span><span class="sxs-lookup"><span data-stu-id="c614a-156">Redis server errors</span></span>

<span data-ttu-id="c614a-157">Quando un server Redis diventa inattivo SignalR , genera eccezioni che indicano che i messaggi non vengono recapitati.</span><span class="sxs-lookup"><span data-stu-id="c614a-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="c614a-158">Alcuni messaggi di eccezione tipici:</span><span class="sxs-lookup"><span data-stu-id="c614a-158">Some typical exception messages:</span></span>

* <span data-ttu-id="c614a-159">*Scrittura del messaggio non riuscita*</span><span class="sxs-lookup"><span data-stu-id="c614a-159">*Failed writing message*</span></span>
* <span data-ttu-id="c614a-160">*Non è stato possibile richiamare il metodo Hub ' MethodName '*</span><span class="sxs-lookup"><span data-stu-id="c614a-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="c614a-161">*Connessione a Redis non riuscita*</span><span class="sxs-lookup"><span data-stu-id="c614a-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="c614a-162">non memorizza nel buffer i messaggi per inviarli quando viene restituito il server.</span><span class="sxs-lookup"><span data-stu-id="c614a-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="c614a-163">Tutti i messaggi inviati durante il server Redis vengono persi.</span><span class="sxs-lookup"><span data-stu-id="c614a-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="c614a-164">si riconnette automaticamente quando il server Redis è nuovamente disponibile.</span><span class="sxs-lookup"><span data-stu-id="c614a-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="c614a-165">Comportamento personalizzato per gli errori di connessione</span><span class="sxs-lookup"><span data-stu-id="c614a-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="c614a-166">Ecco un esempio che illustra come gestire gli eventi di errore di connessione Redis.</span><span class="sxs-lookup"><span data-stu-id="c614a-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="c614a-167">Clustering di redis</span><span class="sxs-lookup"><span data-stu-id="c614a-167">Redis Clustering</span></span>

<span data-ttu-id="c614a-168">Il [clustering di redis](https://redis.io/topics/cluster-spec) è un metodo per ottenere la disponibilità elevata usando più server Redis.</span><span class="sxs-lookup"><span data-stu-id="c614a-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="c614a-169">Il clustering non è ufficialmente supportato, ma potrebbe funzionare.</span><span class="sxs-lookup"><span data-stu-id="c614a-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c614a-170">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="c614a-170">Next steps</span></span>

<span data-ttu-id="c614a-171">Per altre informazioni, vedere le seguenti risorse:</span><span class="sxs-lookup"><span data-stu-id="c614a-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="c614a-172">Documentazione di Redis</span><span class="sxs-lookup"><span data-stu-id="c614a-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="c614a-173">Documentazione di StackExchange Redis</span><span class="sxs-lookup"><span data-stu-id="c614a-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="c614a-174">Documentazione di Cache Redis di Azure</span><span class="sxs-lookup"><span data-stu-id="c614a-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
