---
title: Helper tag di cache distribuita in ASP.NET Core
author: pkellner
description: Informazioni su come usare l'helper tag di cache distribuita.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 04ab5be4d9cec066a4b7cd422a1566bcbb5a291a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061158"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="bc7b4-103">Helper tag di cache distribuita in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bc7b4-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="bc7b4-104">Di [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="bc7b4-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="bc7b4-105">L'helper tag di cache distribuita consente di migliorare notevolmente le prestazioni dell'app ASP.NET Core memorizzandone il contenuto in un'origine cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="bc7b4-106">Per una panoramica degli helper per tag, vedere <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="bc7b4-107">L'helper tag di cache distribuita eredita dalla stessa classe di base da cui eredita l'helper tag di cache.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="bc7b4-108">Tutti gli attributi dell'[helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) sono disponibili per l'helper tag di cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="bc7b4-109">L'helper tag di cache distribuita usa l'[inserimento del costruttore](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span><span class="sxs-lookup"><span data-stu-id="bc7b4-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="bc7b4-110">L'interfaccia <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> viene passata nel costruttore dell'helper tag di cache distribuita.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="bc7b4-111">Se non viene creata alcuna implementazione concreta di `IDistributedCache` in `Startup.ConfigureServices` ( *Startup.cs* ), l'helper tag di cache distribuita usa lo stesso provider in memoria per l'archiviazione dei dati memorizzati nella cache dell' [helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bc7b4-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` ( *Startup.cs* ), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="bc7b4-112">Attributi dell'helper per tag di cache distribuita</span><span class="sxs-lookup"><span data-stu-id="bc7b4-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="bc7b4-113">Attributi condivisi con l'helper tag di cache</span><span class="sxs-lookup"><span data-stu-id="bc7b4-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="bc7b4-114">L'helper tag di cache distribuita eredita dalla stessa classe dell'helper tag di cache.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="bc7b4-115">Per una descrizione di questi attributi, vedere [Helper tag di cache](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="bc7b4-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="bc7b4-116">name</span><span class="sxs-lookup"><span data-stu-id="bc7b4-116">name</span></span>

| <span data-ttu-id="bc7b4-117">Tipo di attributo</span><span class="sxs-lookup"><span data-stu-id="bc7b4-117">Attribute Type</span></span> | <span data-ttu-id="bc7b4-118">Esempio</span><span class="sxs-lookup"><span data-stu-id="bc7b4-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="bc7b4-119">string</span><span class="sxs-lookup"><span data-stu-id="bc7b4-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="bc7b4-120">`name` è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-120">`name` is required.</span></span> <span data-ttu-id="bc7b4-121">L'attributo `name` viene usato come chiave per ogni istanza di cache archiviata.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="bc7b4-122">Diversamente dall'helper tag di cache che assegna una chiave di cache a ogni istanza in base al Razor nome e alla posizione della pagina Razor , l'helper tag di cache distribuita basa solo la relativa chiave sull'attributo `name` .</span><span class="sxs-lookup"><span data-stu-id="bc7b4-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="bc7b4-123">Esempio:</span><span class="sxs-lookup"><span data-stu-id="bc7b4-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="bc7b4-124">Implementazioni IDistributedCache dell'helper tag di cache distribuita</span><span class="sxs-lookup"><span data-stu-id="bc7b4-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="bc7b4-125">Esistono due implementazioni di <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> integrate in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="bc7b4-126">Una è basata su SQL Server e l'altra su Redis.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="bc7b4-127">Sono inoltre disponibili implementazioni di terze parti, ad esempio [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span><span class="sxs-lookup"><span data-stu-id="bc7b4-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="bc7b4-128">I dettagli di queste implementazioni sono disponibili in <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="bc7b4-129">Entrambe le implementazioni implicano l'impostazione di un'istanza di `IDistributedCache` in `Startup`.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="bc7b4-130">Nessun attributo di tag è associato specificamente all'uso di un'implementazione specifica di `IDistributedCache`.</span><span class="sxs-lookup"><span data-stu-id="bc7b4-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bc7b4-131">Risorse aggiuntive</span><span class="sxs-lookup"><span data-stu-id="bc7b4-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
