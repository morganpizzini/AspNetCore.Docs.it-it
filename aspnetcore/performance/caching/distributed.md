---
title: Caching distribuito in ASP.NET Core
author: rick-anderson
description: Informazioni su come usare una cache distribuita ASP.NET Core per migliorare le prestazioni e la scalabilità delle app, soprattutto in un ambiente cloud o server farm.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: a25cbaf9a4e7dc5f1bd3706d01f409208a39aaa3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626727"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="6a9bf-103">Caching distribuito in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a9bf-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="6a9bf-104">Di e di Stevie [Nasir](https://github.com/mohsinnasir) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6a9bf-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a9bf-105">Una cache distribuita è una cache condivisa da più server app, in genere gestita come servizio esterno ai server app che vi accedono.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6a9bf-106">Una cache distribuita può migliorare le prestazioni e la scalabilità di un'app ASP.NET Core, soprattutto quando l'app è ospitata da un servizio cloud o da un server farm.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6a9bf-107">Una cache distribuita presenta diversi vantaggi rispetto ad altri scenari di memorizzazione nella cache in cui i dati memorizzati nella cache vengono archiviati nei singoli server dell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6a9bf-108">Quando vengono distribuiti i dati memorizzati nella cache, i dati:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6a9bf-109">È *coerente* (coerente) tra le richieste a più server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6a9bf-110">Sopravvive ai riavvii del server e alle distribuzioni di app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6a9bf-111">Non usa la memoria locale.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-111">Doesn't use local memory.</span></span>

<span data-ttu-id="6a9bf-112">La configurazione della cache distribuita è specifica dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6a9bf-113">Questo articolo descrive come configurare le cache distribuite SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6a9bf-114">Sono disponibili anche implementazioni di terze parti, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6a9bf-115">Indipendentemente dall'implementazione selezionata, l'app interagisce con la cache usando l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6a9bf-116">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a9bf-117">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-117">Prerequisites</span></span>

<span data-ttu-id="6a9bf-118">Per utilizzare una SQL Server cache distribuita, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6a9bf-119">Per usare una cache distribuita Redis, aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="6a9bf-120">Per usare la cache distribuita NCache, aggiungere un riferimento al pacchetto [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6a9bf-121">Interfaccia IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6a9bf-121">IDistributedCache interface</span></span>

<span data-ttu-id="6a9bf-122">L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia fornisce i metodi seguenti per modificare gli elementi nell'implementazione della cache distribuita:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6a9bf-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accetta una chiave di stringa e recupera un elemento memorizzato nella cache come `byte[]` matrice se viene trovato nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6a9bf-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Aggiunge un elemento (come `byte[]` matrice) alla cache usando una chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6a9bf-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aggiorna un elemento nella cache in base alla relativa chiave, reimpostando il timeout di scadenza variabile (se presente).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6a9bf-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Rimuove un elemento della cache in base alla relativa chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6a9bf-127">Stabilire servizi di memorizzazione nella cache distribuiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-127">Establish distributed caching services</span></span>

<span data-ttu-id="6a9bf-128">Registrare un'implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a9bf-129">Le implementazioni fornite dal Framework descritte in questo argomento includono:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6a9bf-130">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6a9bf-131">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6a9bf-132">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6a9bf-133">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6a9bf-134">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-134">Distributed Memory Cache</span></span>

<span data-ttu-id="6a9bf-135">La cache di memoria distribuita ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) è un'implementazione fornita dal framework di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> che archivia gli elementi in memoria.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6a9bf-136">La cache di memoria distribuita non è una cache distribuita effettiva.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6a9bf-137">Gli elementi memorizzati nella cache vengono archiviati dall'istanza dell'app nel server in cui è in esecuzione l'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6a9bf-138">La cache di memoria distribuita è un'implementazione utile:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6a9bf-139">Negli scenari di sviluppo e test.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="6a9bf-140">Quando un singolo server viene utilizzato nell'ambiente di produzione e l'utilizzo della memoria non costituisce un problema.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6a9bf-141">L'implementazione della cache di memoria distribuita astrae l'archiviazione dei dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6a9bf-142">Consente di implementare in futuro una soluzione di memorizzazione nella cache distribuita se più nodi o tolleranza di errore diventano necessari.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6a9bf-143">L'app di esempio usa la cache di memoria distribuita quando l'app viene eseguita nell'ambiente di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6a9bf-144">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6a9bf-145">L'implementazione della cache SQL Server distribuita ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) consente alla cache distribuita di usare un database SQL Server come archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6a9bf-146">Per creare una tabella SQL Server elemento memorizzato nella cache in un'istanza di SQL Server, è possibile utilizzare lo `sql-cache` strumento.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6a9bf-147">Lo strumento crea una tabella con il nome e lo schema specificati.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6a9bf-148">Creare una tabella in SQL Server eseguendo il `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6a9bf-149">Fornire l'istanza di SQL Server ( `Data Source` ), il database ( `Initial Catalog` ), lo schema (ad esempio, `dbo` ) e il nome della tabella (ad esempio, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6a9bf-150">Viene registrato un messaggio per indicare che lo strumento è stato completato correttamente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6a9bf-151">La tabella creata dallo `sql-cache` strumento presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabella cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6a9bf-153">Un'app deve modificare i valori della cache usando un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , non un oggetto <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6a9bf-154">L'app di esempio implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in un ambiente non di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6a9bf-155">Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, facoltativamente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) vengono in genere archiviati all'esterno del controllo del codice sorgente (ad esempio, archiviati dal [gestore dei segreti](xref:security/app-secrets) o in *appsettings.jssu* / *appSettings. { ENVIRONMENT} file JSON* ).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6a9bf-156">La stringa di connessione può contenere credenziali che devono essere mantenute fuori dai sistemi di controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6a9bf-157">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-157">Distributed Redis Cache</span></span>

<span data-ttu-id="6a9bf-158">[Redis](https://redis.io/) è un archivio dati in memoria open source, che viene spesso usato come cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6a9bf-159">È possibile usare Redis localmente ed è possibile configurare una [cache Redis di Azure](https://azure.microsoft.com/services/cache/) per un'app ASP.NET Core ospitata in Azure.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6a9bf-160">Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> istanza ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) in un ambiente non di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="6a9bf-161">Per installare Redis nel computer locale:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6a9bf-162">Installare il [pacchetto di cioccolaty Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6a9bf-163">Eseguire `redis-server` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6a9bf-164">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-164">Distributed NCache Cache</span></span>

<span data-ttu-id="6a9bf-165">[NCache](https://github.com/Alachisoft/NCache) è una cache distribuita in memoria open source sviluppata in modo nativo in .NET e .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6a9bf-166">NCache funziona sia localmente che configurato come cluster di cache distribuita per un'app ASP.NET Core in esecuzione in Azure o in altre piattaforme di hosting.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6a9bf-167">Per installare e configurare NCache nel computer locale, vedere [NCache Introduzione Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6a9bf-168">Per configurare NCache:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-168">To configure NCache:</span></span>

1. <span data-ttu-id="6a9bf-169">Installare [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6a9bf-170">Configurare il cluster di cache in [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6a9bf-171">Aggiungere il codice seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6a9bf-172">Usare la cache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-172">Use the distributed cache</span></span>

<span data-ttu-id="6a9bf-173">Per usare l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia, richiedere un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da qualsiasi costruttore nell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6a9bf-174">L'istanza viene fornita da [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6a9bf-175">Quando l'app di esempio viene avviata, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene inserita in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6a9bf-176">L'ora corrente viene memorizzata nella cache usando <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (per altre informazioni, vedere [host generico: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6a9bf-177">L'app di esempio inserisce <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nell'oggetto `IndexModel` per l'uso da parte della pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6a9bf-178">Ogni volta che viene caricata la pagina di indice, viene verificata la presenza dell'ora memorizzata nella cache in `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6a9bf-179">Se il tempo di memorizzazione nella cache non è scaduto, viene visualizzata l'ora.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6a9bf-180">Se sono trascorsi 20 secondi dall'ultima volta in cui è stato eseguito l'accesso all'ora memorizzata nella cache (l'ultima volta che questa pagina è stata caricata), la pagina Visualizza l' *ora della cache scaduta*.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6a9bf-181">Aggiornare immediatamente l'ora memorizzata nella cache all'ora corrente selezionando il pulsante **Reimposta tempo memorizzato nella cache** .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6a9bf-182">Il pulsante attiva il `OnPostResetCachedTime` metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6a9bf-183">Non è necessario usare un singleton o una durata con ambito per <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le istanze (almeno per le implementazioni predefinite).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6a9bf-184">È anche possibile creare un' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> istanza di ogni volta che è necessario invece di usare, ma la creazione di un'istanza nel codice può rendere il codice più difficile da testare e violare il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6a9bf-185">Consigli</span><span class="sxs-lookup"><span data-stu-id="6a9bf-185">Recommendations</span></span>

<span data-ttu-id="6a9bf-186">Quando si decide quale implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> è migliore per l'app, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6a9bf-187">Infrastruttura esistente</span><span class="sxs-lookup"><span data-stu-id="6a9bf-187">Existing infrastructure</span></span>
* <span data-ttu-id="6a9bf-188">Requisiti per le prestazioni</span><span class="sxs-lookup"><span data-stu-id="6a9bf-188">Performance requirements</span></span>
* <span data-ttu-id="6a9bf-189">Cost</span><span class="sxs-lookup"><span data-stu-id="6a9bf-189">Cost</span></span>
* <span data-ttu-id="6a9bf-190">Esperienza del team</span><span class="sxs-lookup"><span data-stu-id="6a9bf-190">Team experience</span></span>

<span data-ttu-id="6a9bf-191">Le soluzioni per la memorizzazione nella cache si basano in genere sull'archiviazione in memoria per consentire un rapido recupero dei dati memorizzati nella cache, ma la memoria è una risorsa limitata ed è dispendiosa per espanderla.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6a9bf-192">Archiviare solo i dati utilizzati di frequente in una cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6a9bf-193">In genere, una cache Redis offre una velocità effettiva più elevata e una latenza inferiore rispetto a una cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6a9bf-194">Tuttavia, il benchmarking è in genere necessario per determinare le caratteristiche di prestazioni delle strategie di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6a9bf-195">Quando SQL Server viene usato come archivio di backup della cache distribuita, l'uso dello stesso database per la cache e l'archiviazione e il recupero dei dati ordinari dell'app possono influire negativamente sulle prestazioni di entrambi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6a9bf-196">Si consiglia di usare un'istanza di SQL Server dedicata per l'archivio di backup della cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a9bf-197">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6a9bf-197">Additional resources</span></span>

* [<span data-ttu-id="6a9bf-198">Cache Redis in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6a9bf-199">Database SQL in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6a9bf-200">[ASP.NET Core provider IDistributedCache per NCache in Web farm](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6a9bf-201">Una cache distribuita è una cache condivisa da più server app, in genere gestita come servizio esterno ai server app che vi accedono.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6a9bf-202">Una cache distribuita può migliorare le prestazioni e la scalabilità di un'app ASP.NET Core, soprattutto quando l'app è ospitata da un servizio cloud o da un server farm.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6a9bf-203">Una cache distribuita presenta diversi vantaggi rispetto ad altri scenari di memorizzazione nella cache in cui i dati memorizzati nella cache vengono archiviati nei singoli server dell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6a9bf-204">Quando vengono distribuiti i dati memorizzati nella cache, i dati:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6a9bf-205">È *coerente* (coerente) tra le richieste a più server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6a9bf-206">Sopravvive ai riavvii del server e alle distribuzioni di app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6a9bf-207">Non usa la memoria locale.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-207">Doesn't use local memory.</span></span>

<span data-ttu-id="6a9bf-208">La configurazione della cache distribuita è specifica dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6a9bf-209">Questo articolo descrive come configurare le cache distribuite SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6a9bf-210">Sono disponibili anche implementazioni di terze parti, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6a9bf-211">Indipendentemente dall'implementazione selezionata, l'app interagisce con la cache usando l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6a9bf-212">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a9bf-213">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-213">Prerequisites</span></span>

<span data-ttu-id="6a9bf-214">Per usare una cache distribuita SQL Server, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6a9bf-215">Per usare una cache distribuita Redis, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="6a9bf-216">Il pacchetto Redis non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto Redis separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="6a9bf-217">Per usare la cache distribuita NCache, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="6a9bf-218">Il pacchetto NCache non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto NCache separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6a9bf-219">Interfaccia IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6a9bf-219">IDistributedCache interface</span></span>

<span data-ttu-id="6a9bf-220">L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia fornisce i metodi seguenti per modificare gli elementi nell'implementazione della cache distribuita:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6a9bf-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accetta una chiave di stringa e recupera un elemento memorizzato nella cache come `byte[]` matrice se viene trovato nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6a9bf-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Aggiunge un elemento (come `byte[]` matrice) alla cache usando una chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6a9bf-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aggiorna un elemento nella cache in base alla relativa chiave, reimpostando il timeout di scadenza variabile (se presente).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6a9bf-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Rimuove un elemento della cache in base alla relativa chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6a9bf-225">Stabilire servizi di memorizzazione nella cache distribuiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-225">Establish distributed caching services</span></span>

<span data-ttu-id="6a9bf-226">Registrare un'implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a9bf-227">Le implementazioni fornite dal Framework descritte in questo argomento includono:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6a9bf-228">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6a9bf-229">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6a9bf-230">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6a9bf-231">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6a9bf-232">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-232">Distributed Memory Cache</span></span>

<span data-ttu-id="6a9bf-233">La cache di memoria distribuita ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) è un'implementazione fornita dal framework di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> che archivia gli elementi in memoria.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6a9bf-234">La cache di memoria distribuita non è una cache distribuita effettiva.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6a9bf-235">Gli elementi memorizzati nella cache vengono archiviati dall'istanza dell'app nel server in cui è in esecuzione l'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6a9bf-236">La cache di memoria distribuita è un'implementazione utile:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6a9bf-237">Negli scenari di sviluppo e test.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="6a9bf-238">Quando un singolo server viene utilizzato nell'ambiente di produzione e l'utilizzo della memoria non costituisce un problema.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6a9bf-239">L'implementazione della cache di memoria distribuita astrae l'archiviazione dei dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6a9bf-240">Consente di implementare in futuro una soluzione di memorizzazione nella cache distribuita se più nodi o tolleranza di errore diventano necessari.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6a9bf-241">L'app di esempio usa la cache di memoria distribuita quando l'app viene eseguita nell'ambiente di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6a9bf-242">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6a9bf-243">L'implementazione della cache SQL Server distribuita ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) consente alla cache distribuita di usare un database SQL Server come archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6a9bf-244">Per creare una tabella SQL Server elemento memorizzato nella cache in un'istanza di SQL Server, è possibile utilizzare lo `sql-cache` strumento.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6a9bf-245">Lo strumento crea una tabella con il nome e lo schema specificati.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6a9bf-246">Creare una tabella in SQL Server eseguendo il `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6a9bf-247">Fornire l'istanza di SQL Server ( `Data Source` ), il database ( `Initial Catalog` ), lo schema (ad esempio, `dbo` ) e il nome della tabella (ad esempio, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6a9bf-248">Viene registrato un messaggio per indicare che lo strumento è stato completato correttamente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6a9bf-249">La tabella creata dallo `sql-cache` strumento presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabella cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6a9bf-251">Un'app deve modificare i valori della cache usando un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , non un oggetto <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6a9bf-252">L'app di esempio implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in un ambiente non di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6a9bf-253">Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, facoltativamente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) vengono in genere archiviati all'esterno del controllo del codice sorgente (ad esempio, archiviati dal [gestore dei segreti](xref:security/app-secrets) o in *appsettings.jssu* / *appSettings. { ENVIRONMENT} file JSON* ).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6a9bf-254">La stringa di connessione può contenere credenziali che devono essere mantenute fuori dai sistemi di controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6a9bf-255">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-255">Distributed Redis Cache</span></span>

<span data-ttu-id="6a9bf-256">[Redis](https://redis.io/) è un archivio dati in memoria open source, che viene spesso usato come cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6a9bf-257">È possibile usare Redis localmente ed è possibile configurare una [cache Redis di Azure](https://azure.microsoft.com/services/cache/) per un'app ASP.NET Core ospitata in Azure.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6a9bf-258">Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> istanza ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) in un ambiente non di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="6a9bf-259">Per installare Redis nel computer locale:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6a9bf-260">Installare il [pacchetto di cioccolaty Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6a9bf-261">Eseguire `redis-server` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6a9bf-262">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-262">Distributed NCache Cache</span></span>

<span data-ttu-id="6a9bf-263">[NCache](https://github.com/Alachisoft/NCache) è una cache distribuita in memoria open source sviluppata in modo nativo in .NET e .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6a9bf-264">NCache funziona sia localmente che configurato come cluster di cache distribuita per un'app ASP.NET Core in esecuzione in Azure o in altre piattaforme di hosting.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6a9bf-265">Per installare e configurare NCache nel computer locale, vedere [NCache Introduzione Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6a9bf-266">Per configurare NCache:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-266">To configure NCache:</span></span>

1. <span data-ttu-id="6a9bf-267">Installare [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6a9bf-268">Configurare il cluster di cache in [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6a9bf-269">Aggiungere il codice seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6a9bf-270">Usare la cache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-270">Use the distributed cache</span></span>

<span data-ttu-id="6a9bf-271">Per usare l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia, richiedere un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da qualsiasi costruttore nell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6a9bf-272">L'istanza viene fornita da [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6a9bf-273">Quando l'app di esempio viene avviata, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene inserita in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6a9bf-274">L'ora corrente viene memorizzata nella cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (per altre informazioni, vedere [Web host: interfaccia IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6a9bf-275">L'app di esempio inserisce <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nell'oggetto `IndexModel` per l'uso da parte della pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6a9bf-276">Ogni volta che viene caricata la pagina di indice, viene verificata la presenza dell'ora memorizzata nella cache in `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6a9bf-277">Se il tempo di memorizzazione nella cache non è scaduto, viene visualizzata l'ora.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6a9bf-278">Se sono trascorsi 20 secondi dall'ultima volta in cui è stato eseguito l'accesso all'ora memorizzata nella cache (l'ultima volta che questa pagina è stata caricata), la pagina Visualizza l' *ora della cache scaduta*.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6a9bf-279">Aggiornare immediatamente l'ora memorizzata nella cache all'ora corrente selezionando il pulsante **Reimposta tempo memorizzato nella cache** .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6a9bf-280">Il pulsante attiva il `OnPostResetCachedTime` metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6a9bf-281">Non è necessario usare un singleton o una durata con ambito per <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le istanze (almeno per le implementazioni predefinite).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6a9bf-282">È anche possibile creare un' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> istanza di ogni volta che è necessario invece di usare, ma la creazione di un'istanza nel codice può rendere il codice più difficile da testare e violare il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6a9bf-283">Consigli</span><span class="sxs-lookup"><span data-stu-id="6a9bf-283">Recommendations</span></span>

<span data-ttu-id="6a9bf-284">Quando si decide quale implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> è migliore per l'app, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6a9bf-285">Infrastruttura esistente</span><span class="sxs-lookup"><span data-stu-id="6a9bf-285">Existing infrastructure</span></span>
* <span data-ttu-id="6a9bf-286">Requisiti per le prestazioni</span><span class="sxs-lookup"><span data-stu-id="6a9bf-286">Performance requirements</span></span>
* <span data-ttu-id="6a9bf-287">Cost</span><span class="sxs-lookup"><span data-stu-id="6a9bf-287">Cost</span></span>
* <span data-ttu-id="6a9bf-288">Esperienza del team</span><span class="sxs-lookup"><span data-stu-id="6a9bf-288">Team experience</span></span>

<span data-ttu-id="6a9bf-289">Le soluzioni per la memorizzazione nella cache si basano in genere sull'archiviazione in memoria per consentire un rapido recupero dei dati memorizzati nella cache, ma la memoria è una risorsa limitata ed è dispendiosa per espanderla.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6a9bf-290">Archiviare solo i dati utilizzati di frequente in una cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6a9bf-291">In genere, una cache Redis offre una velocità effettiva più elevata e una latenza inferiore rispetto a una cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6a9bf-292">Tuttavia, il benchmarking è in genere necessario per determinare le caratteristiche di prestazioni delle strategie di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6a9bf-293">Quando SQL Server viene usato come archivio di backup della cache distribuita, l'uso dello stesso database per la cache e l'archiviazione e il recupero dei dati ordinari dell'app possono influire negativamente sulle prestazioni di entrambi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6a9bf-294">Si consiglia di usare un'istanza di SQL Server dedicata per l'archivio di backup della cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a9bf-295">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6a9bf-295">Additional resources</span></span>

* [<span data-ttu-id="6a9bf-296">Cache Redis in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6a9bf-297">Database SQL in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6a9bf-298">[ASP.NET Core provider IDistributedCache per NCache in Web farm](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="6a9bf-299">Una cache distribuita è una cache condivisa da più server app, in genere gestita come servizio esterno ai server app che vi accedono.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="6a9bf-300">Una cache distribuita può migliorare le prestazioni e la scalabilità di un'app ASP.NET Core, soprattutto quando l'app è ospitata da un servizio cloud o da un server farm.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="6a9bf-301">Una cache distribuita presenta diversi vantaggi rispetto ad altri scenari di memorizzazione nella cache in cui i dati memorizzati nella cache vengono archiviati nei singoli server dell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="6a9bf-302">Quando vengono distribuiti i dati memorizzati nella cache, i dati:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="6a9bf-303">È *coerente* (coerente) tra le richieste a più server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="6a9bf-304">Sopravvive ai riavvii del server e alle distribuzioni di app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="6a9bf-305">Non usa la memoria locale.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-305">Doesn't use local memory.</span></span>

<span data-ttu-id="6a9bf-306">La configurazione della cache distribuita è specifica dell'implementazione.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="6a9bf-307">Questo articolo descrive come configurare le cache distribuite SQL Server e Redis.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="6a9bf-308">Sono disponibili anche implementazioni di terze parti, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache)).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="6a9bf-309">Indipendentemente dall'implementazione selezionata, l'app interagisce con la cache usando l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="6a9bf-310">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a9bf-311">Prerequisiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-311">Prerequisites</span></span>

<span data-ttu-id="6a9bf-312">Per usare una cache distribuita SQL Server, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) o aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="6a9bf-313">Per usare una cache distribuita Redis, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [Microsoft. Extensions. Caching. Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="6a9bf-314">Il pacchetto Redis non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto Redis separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="6a9bf-315">Per usare la cache distribuita NCache, fare riferimento al [metapacchetto Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) e aggiungere un riferimento al pacchetto [NCache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="6a9bf-316">Il pacchetto NCache non è incluso nel `Microsoft.AspNetCore.App` pacchetto, quindi è necessario fare riferimento al pacchetto NCache separatamente nel file di progetto.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="6a9bf-317">Interfaccia IDistributedCache</span><span class="sxs-lookup"><span data-stu-id="6a9bf-317">IDistributedCache interface</span></span>

<span data-ttu-id="6a9bf-318">L' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia fornisce i metodi seguenti per modificare gli elementi nell'implementazione della cache distribuita:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="6a9bf-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Accetta una chiave di stringa e recupera un elemento memorizzato nella cache come `byte[]` matrice se viene trovato nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="6a9bf-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Aggiunge un elemento (come `byte[]` matrice) alla cache usando una chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="6a9bf-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aggiorna un elemento nella cache in base alla relativa chiave, reimpostando il timeout di scadenza variabile (se presente).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="6a9bf-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Rimuove un elemento della cache in base alla relativa chiave di stringa.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="6a9bf-323">Stabilire servizi di memorizzazione nella cache distribuiti</span><span class="sxs-lookup"><span data-stu-id="6a9bf-323">Establish distributed caching services</span></span>

<span data-ttu-id="6a9bf-324">Registrare un'implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a9bf-325">Le implementazioni fornite dal Framework descritte in questo argomento includono:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="6a9bf-326">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="6a9bf-327">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="6a9bf-328">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="6a9bf-329">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="6a9bf-330">Cache di memoria distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-330">Distributed Memory Cache</span></span>

<span data-ttu-id="6a9bf-331">La cache di memoria distribuita ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) è un'implementazione fornita dal framework di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> che archivia gli elementi in memoria.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="6a9bf-332">La cache di memoria distribuita non è una cache distribuita effettiva.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="6a9bf-333">Gli elementi memorizzati nella cache vengono archiviati dall'istanza dell'app nel server in cui è in esecuzione l'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="6a9bf-334">La cache di memoria distribuita è un'implementazione utile:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="6a9bf-335">Negli scenari di sviluppo e test.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="6a9bf-336">Quando un singolo server viene utilizzato nell'ambiente di produzione e l'utilizzo della memoria non costituisce un problema.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="6a9bf-337">L'implementazione della cache di memoria distribuita astrae l'archiviazione dei dati memorizzati nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="6a9bf-338">Consente di implementare in futuro una soluzione di memorizzazione nella cache distribuita se più nodi o tolleranza di errore diventano necessari.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="6a9bf-339">L'app di esempio usa la cache di memoria distribuita quando l'app viene eseguita nell'ambiente di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="6a9bf-340">Cache SQL Server distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="6a9bf-341">L'implementazione della cache SQL Server distribuita ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) consente alla cache distribuita di usare un database SQL Server come archivio di backup.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="6a9bf-342">Per creare una tabella SQL Server elemento memorizzato nella cache in un'istanza di SQL Server, è possibile utilizzare lo `sql-cache` strumento.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="6a9bf-343">Lo strumento crea una tabella con il nome e lo schema specificati.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="6a9bf-344">Creare una tabella in SQL Server eseguendo il `sql-cache create` comando.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="6a9bf-345">Fornire l'istanza di SQL Server ( `Data Source` ), il database ( `Initial Catalog` ), lo schema (ad esempio, `dbo` ) e il nome della tabella (ad esempio, `TestCache` ):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="6a9bf-346">Viene registrato un messaggio per indicare che lo strumento è stato completato correttamente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="6a9bf-347">La tabella creata dallo `sql-cache` strumento presenta lo schema seguente:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![Tabella cache SqlServer](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="6a9bf-349">Un'app deve modificare i valori della cache usando un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , non un oggetto <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="6a9bf-350">L'app di esempio implementa <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in un ambiente non di sviluppo in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6a9bf-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="6a9bf-351">Un <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (e, facoltativamente, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> e <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) vengono in genere archiviati all'esterno del controllo del codice sorgente (ad esempio, archiviati dal [gestore dei segreti](xref:security/app-secrets) o in *appsettings.jssu* / *appSettings. { ENVIRONMENT} file JSON* ).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="6a9bf-352">La stringa di connessione può contenere credenziali che devono essere mantenute fuori dai sistemi di controllo del codice sorgente.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="6a9bf-353">Cache Redis distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-353">Distributed Redis Cache</span></span>

<span data-ttu-id="6a9bf-354">[Redis](https://redis.io/) è un archivio dati in memoria open source, che viene spesso usato come cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="6a9bf-355">È possibile usare Redis localmente ed è possibile configurare una [cache Redis di Azure](https://azure.microsoft.com/services/cache/) per un'app ASP.NET Core ospitata in Azure.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="6a9bf-356">Un'app configura l'implementazione della cache usando un' <xref:Microsoft.Extensions.Caching.Redis.RedisCache> istanza di ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="6a9bf-357">Per installare Redis nel computer locale:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="6a9bf-358">Installare il [pacchetto di cioccolaty Redis](https://chocolatey.org/packages/redis-64/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="6a9bf-359">Eseguire `redis-server` da un prompt dei comandi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="6a9bf-360">Cache NCache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-360">Distributed NCache Cache</span></span>

<span data-ttu-id="6a9bf-361">[NCache](https://github.com/Alachisoft/NCache) è una cache distribuita in memoria open source sviluppata in modo nativo in .NET e .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="6a9bf-362">NCache funziona sia localmente che configurato come cluster di cache distribuita per un'app ASP.NET Core in esecuzione in Azure o in altre piattaforme di hosting.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="6a9bf-363">Per installare e configurare NCache nel computer locale, vedere [NCache Introduzione Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="6a9bf-364">Per configurare NCache:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-364">To configure NCache:</span></span>

1. <span data-ttu-id="6a9bf-365">Installare [NCache Open Source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="6a9bf-366">Configurare il cluster di cache in [client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="6a9bf-367">Aggiungere il codice seguente a `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="6a9bf-368">Usare la cache distribuita</span><span class="sxs-lookup"><span data-stu-id="6a9bf-368">Use the distributed cache</span></span>

<span data-ttu-id="6a9bf-369">Per usare l' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interfaccia, richiedere un'istanza di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> da qualsiasi costruttore nell'app.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="6a9bf-370">L'istanza viene fornita da [inserimento delle dipendenze (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6a9bf-371">Quando l'app di esempio viene avviata, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene inserita in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="6a9bf-372">L'ora corrente viene memorizzata nella cache usando <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (per altre informazioni, vedere [Web host: interfaccia IApplicationLifetime](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="6a9bf-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="6a9bf-373">L'app di esempio inserisce <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> nell'oggetto `IndexModel` per l'uso da parte della pagina di indice.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="6a9bf-374">Ogni volta che viene caricata la pagina di indice, viene verificata la presenza dell'ora memorizzata nella cache in `OnGetAsync` .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="6a9bf-375">Se il tempo di memorizzazione nella cache non è scaduto, viene visualizzata l'ora.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="6a9bf-376">Se sono trascorsi 20 secondi dall'ultima volta in cui è stato eseguito l'accesso all'ora memorizzata nella cache (l'ultima volta che questa pagina è stata caricata), la pagina Visualizza l' *ora della cache scaduta*.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="6a9bf-377">Aggiornare immediatamente l'ora memorizzata nella cache all'ora corrente selezionando il pulsante **Reimposta tempo memorizzato nella cache** .</span><span class="sxs-lookup"><span data-stu-id="6a9bf-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="6a9bf-378">Il pulsante attiva il `OnPostResetCachedTime` metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="6a9bf-379">Non è necessario usare un singleton o una durata con ambito per <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> le istanze (almeno per le implementazioni predefinite).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="6a9bf-380">È anche possibile creare un' <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> istanza di ogni volta che è necessario invece di usare, ma la creazione di un'istanza nel codice può rendere il codice più difficile da testare e violare il [principio delle dipendenze esplicite](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span><span class="sxs-lookup"><span data-stu-id="6a9bf-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="6a9bf-381">Consigli</span><span class="sxs-lookup"><span data-stu-id="6a9bf-381">Recommendations</span></span>

<span data-ttu-id="6a9bf-382">Quando si decide quale implementazione di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> è migliore per l'app, tenere presente quanto segue:</span><span class="sxs-lookup"><span data-stu-id="6a9bf-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="6a9bf-383">Infrastruttura esistente</span><span class="sxs-lookup"><span data-stu-id="6a9bf-383">Existing infrastructure</span></span>
* <span data-ttu-id="6a9bf-384">Requisiti per le prestazioni</span><span class="sxs-lookup"><span data-stu-id="6a9bf-384">Performance requirements</span></span>
* <span data-ttu-id="6a9bf-385">Cost</span><span class="sxs-lookup"><span data-stu-id="6a9bf-385">Cost</span></span>
* <span data-ttu-id="6a9bf-386">Esperienza del team</span><span class="sxs-lookup"><span data-stu-id="6a9bf-386">Team experience</span></span>

<span data-ttu-id="6a9bf-387">Le soluzioni per la memorizzazione nella cache si basano in genere sull'archiviazione in memoria per consentire un rapido recupero dei dati memorizzati nella cache, ma la memoria è una risorsa limitata ed è dispendiosa per espanderla.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="6a9bf-388">Archiviare solo i dati utilizzati di frequente in una cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="6a9bf-389">In genere, una cache Redis offre una velocità effettiva più elevata e una latenza inferiore rispetto a una cache SQL Server.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="6a9bf-390">Tuttavia, il benchmarking è in genere necessario per determinare le caratteristiche di prestazioni delle strategie di memorizzazione nella cache.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="6a9bf-391">Quando SQL Server viene usato come archivio di backup della cache distribuita, l'uso dello stesso database per la cache e l'archiviazione e il recupero dei dati ordinari dell'app possono influire negativamente sulle prestazioni di entrambi.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="6a9bf-392">Si consiglia di usare un'istanza di SQL Server dedicata per l'archivio di backup della cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="6a9bf-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a9bf-393">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="6a9bf-393">Additional resources</span></span>

* [<span data-ttu-id="6a9bf-394">Cache Redis in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="6a9bf-395">Database SQL in Azure</span><span class="sxs-lookup"><span data-stu-id="6a9bf-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="6a9bf-396">[ASP.NET Core provider IDistributedCache per NCache in Web farm](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache su GitHub](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="6a9bf-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 