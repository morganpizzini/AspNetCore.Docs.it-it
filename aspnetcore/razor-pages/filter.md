---
title: Metodi di filtro per le Razor pagine in ASP.NET Core
author: Rick-Anderson
description: Informazioni su come creare metodi di filtro per le Razor pagine in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: b8942020e98d0f985e5445bb1816c540bcb7829b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021406"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="c1955-103">Metodi di filtro per le Razor pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1955-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c1955-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1955-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c1955-105">RazorI filtri di pagina [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) consentono Razor alle pagine di eseguire codice prima e dopo l'esecuzione di un Razor gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="c1955-106">RazorI filtri di pagina sono simili a [ASP.NET Core filtri azione MVC](xref:mvc/controllers/filters#action-filters), ma non possono essere applicati ai singoli metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="c1955-107">RazorFiltri di pagina:</span><span class="sxs-lookup"><span data-stu-id="c1955-107">Razor Page filters:</span></span>

* <span data-ttu-id="c1955-108">Eseguono il codice dopo aver selezionato un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="c1955-109">Eseguono il codice prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="c1955-110">Eseguono il codice dopo l'esecuzione del metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="c1955-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="c1955-111">Possono essere implementati in una pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="c1955-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="c1955-112">Non possono essere applicati a metodi del gestore pagina specifici.</span><span class="sxs-lookup"><span data-stu-id="c1955-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="c1955-113">Le dipendenze del costruttore possono essere popolate dall' [inserimento di dipendenze](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="c1955-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="c1955-114">Per ulteriori informazioni, vedere [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) e [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="c1955-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="c1955-115">Mentre i costruttori di pagina e il middleware abilitano l'esecuzione di codice personalizzato prima dell'esecuzione di un metodo del gestore, solo Razor i filtri di pagina consentono l'accesso a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> e alla pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="c1955-116">Il middleware ha accesso a `HttpContext` , ma non al "contesto di pagina".</span><span class="sxs-lookup"><span data-stu-id="c1955-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="c1955-117">I filtri hanno un <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametro derivato, che fornisce l'accesso a `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="c1955-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="c1955-118">Di seguito è riportato un esempio per un filtro di pagina: [implementare un attributo di filtro](#ifa) che aggiunge un'intestazione alla risposta, operazione che non può essere eseguita con i costruttori o il middleware.</span><span class="sxs-lookup"><span data-stu-id="c1955-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="c1955-119">L'accesso al contesto della pagina, che include l'accesso alle istanze della pagina e del relativo modello, è disponibile solo quando si eseguono filtri, gestori o il corpo di una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a Razor Page.</span></span>

<span data-ttu-id="c1955-120">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1955-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c1955-121">RazorI filtri di pagina forniscono i metodi seguenti, che possono essere applicati a livello globale o a livello di pagina:</span><span class="sxs-lookup"><span data-stu-id="c1955-121">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="c1955-122">Metodi sincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-122">Synchronous methods:</span></span>

  * <span data-ttu-id="c1955-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): metodo chiamato dopo la selezione di un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="c1955-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): metodo chiamato prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="c1955-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): metodo chiamato dopo che il metodo del gestore è stato eseguito e prima del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="c1955-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="c1955-126">Metodi asincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="c1955-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono dopo la selezione del metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="c1955-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono prima che venga richiamato il metodo del gestore, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="c1955-129">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="c1955-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="c1955-130">Il framework controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="c1955-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="c1955-131">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="c1955-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="c1955-132">Se entrambe le interfacce sono implementate, verranno chiamati solo i metodi asincroni.</span><span class="sxs-lookup"><span data-stu-id="c1955-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="c1955-133">La stessa regola vale per gli override nelle pagine. Implementare la versione sincrona o la versione asincrona dell'override, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="c1955-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="c1955-134">Implementare i Razor filtri di pagina a livello globale</span><span class="sxs-lookup"><span data-stu-id="c1955-134">Implement Razor Page filters globally</span></span>

<span data-ttu-id="c1955-135">Il codice seguente implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="c1955-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="c1955-136">Nel codice precedente, `ProcessUserAgent.Write` è il codice fornito dall'utente che funziona con la stringa agente utente.</span><span class="sxs-lookup"><span data-stu-id="c1955-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="c1955-137">Il codice seguente abilita `SampleAsyncPageFilter` nella classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c1955-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="c1955-138">Il codice seguente chiama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per applicare l'oggetto `SampleAsyncPageFilter` solo alle pagine in */Movies*:</span><span class="sxs-lookup"><span data-stu-id="c1955-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="c1955-139">Il codice seguente implementa il filtro `IPageFilter` sincrono:</span><span class="sxs-lookup"><span data-stu-id="c1955-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="c1955-140">Il codice seguente abilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="c1955-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="c1955-141">Implementare i Razor filtri di pagina eseguendo l'override dei metodi di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-141">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="c1955-142">Il codice seguente esegue l'override dei Razor filtri di pagina asincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-142">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="c1955-143">Implementare un attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-143">Implement a filter attribute</span></span>

<span data-ttu-id="c1955-144">Il filtro di filtro basato su attributi predefinito <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> può essere sottoclassato.</span><span class="sxs-lookup"><span data-stu-id="c1955-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="c1955-145">Il filtro seguente aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="c1955-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="c1955-146">Il codice seguente applica l'attributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="c1955-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="c1955-147">Usare uno strumento come gli strumenti di sviluppo del browser per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="c1955-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="c1955-148">In **intestazioni risposta** `author: Rick` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="c1955-148">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="c1955-149">Vedere [Override dell'ordine predefinito](xref:mvc/controllers/filters#overriding-the-default-order) per istruzioni su come eseguire l'override dell'ordine.</span><span class="sxs-lookup"><span data-stu-id="c1955-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="c1955-150">Vedere [Annullamento e blocco](xref:mvc/controllers/filters#cancellation-and-short-circuiting) per istruzioni su come bloccare la pipeline filtro da un filtro.</span><span class="sxs-lookup"><span data-stu-id="c1955-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="c1955-151">Autorizzare l'attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-151">Authorize filter attribute</span></span>

<span data-ttu-id="c1955-152">L'attributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) può essere applicato a `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="c1955-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c1955-153">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1955-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c1955-154">RazorI filtri di pagina [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) consentono Razor alle pagine di eseguire codice prima e dopo l'esecuzione di un Razor gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-154">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="c1955-155">RazorI filtri di pagina sono simili a [ASP.NET Core filtri azione MVC](xref:mvc/controllers/filters#action-filters), ma non possono essere applicati ai singoli metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="c1955-155">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="c1955-156">RazorFiltri di pagina:</span><span class="sxs-lookup"><span data-stu-id="c1955-156">Razor Page filters:</span></span>

* <span data-ttu-id="c1955-157">Eseguono il codice dopo aver selezionato un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="c1955-158">Eseguono il codice prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="c1955-159">Eseguono il codice dopo l'esecuzione del metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="c1955-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="c1955-160">Possono essere implementati in una pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="c1955-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="c1955-161">Non possono essere applicati a metodi del gestore pagina specifici.</span><span class="sxs-lookup"><span data-stu-id="c1955-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="c1955-162">Il codice può essere eseguito prima dell'esecuzione di un metodo del gestore usando il costruttore o il middleware della pagina, ma solo i Razor filtri di pagina hanno accesso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="c1955-162">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="c1955-163">I filtri contengono un parametro derivato da [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), che fornisce accesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="c1955-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="c1955-164">Ad esempio, nell'esempio [Implementare un attributo di filtro](#ifa) viene aggiunta un'intestazione alla risposta, operazione impossibile con costruttori o middleware.</span><span class="sxs-lookup"><span data-stu-id="c1955-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="c1955-165">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c1955-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c1955-166">RazorI filtri di pagina forniscono i metodi seguenti, che possono essere applicati a livello globale o a livello di pagina:</span><span class="sxs-lookup"><span data-stu-id="c1955-166">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="c1955-167">Metodi sincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-167">Synchronous methods:</span></span>

  * <span data-ttu-id="c1955-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): metodo chiamato dopo la selezione di un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="c1955-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): metodo chiamato prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="c1955-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): metodo chiamato dopo che il metodo del gestore è stato eseguito e prima del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="c1955-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="c1955-171">Metodi asincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="c1955-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono dopo la selezione del metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="c1955-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono prima che venga richiamato il metodo del gestore, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="c1955-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="c1955-174">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="c1955-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="c1955-175">Il framework controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="c1955-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="c1955-176">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="c1955-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="c1955-177">Se entrambe le interfacce sono implementate, verranno chiamati solo i metodi asincroni.</span><span class="sxs-lookup"><span data-stu-id="c1955-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="c1955-178">La stessa regola vale per gli override nelle pagine. Implementare la versione sincrona o la versione asincrona dell'override, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="c1955-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="c1955-179">Implementare i Razor filtri di pagina a livello globale</span><span class="sxs-lookup"><span data-stu-id="c1955-179">Implement Razor Page filters globally</span></span>

<span data-ttu-id="c1955-180">Il codice seguente implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="c1955-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="c1955-181">Nel codice precedente, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="c1955-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="c1955-182">Nell'esempio viene utilizzato per fornire informazioni di traccia per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="c1955-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="c1955-183">Il codice seguente abilita `SampleAsyncPageFilter` nella classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="c1955-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="c1955-184">Il codice seguente mostra la classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="c1955-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="c1955-185">Il codice seguente chiama `AddFolderApplicationModelConvention` per applicare `SampleAsyncPageFilter` solo alle pagine in */subFolder*:</span><span class="sxs-lookup"><span data-stu-id="c1955-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="c1955-186">Il codice seguente implementa il filtro `IPageFilter` sincrono:</span><span class="sxs-lookup"><span data-stu-id="c1955-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="c1955-187">Il codice seguente abilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="c1955-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="c1955-188">Implementare i Razor filtri di pagina eseguendo l'override dei metodi di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-188">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="c1955-189">Il codice seguente esegue l'override dei Razor filtri di pagina sincroni:</span><span class="sxs-lookup"><span data-stu-id="c1955-189">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="c1955-190">Implementare un attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-190">Implement a filter attribute</span></span>

<span data-ttu-id="c1955-191">Il filtro basato su attributi predefinito [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) può essere sottoclassato.</span><span class="sxs-lookup"><span data-stu-id="c1955-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="c1955-192">Il filtro seguente aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="c1955-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="c1955-193">Il codice seguente applica l'attributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="c1955-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="c1955-194">Vedere [Override dell'ordine predefinito](xref:mvc/controllers/filters#overriding-the-default-order) per istruzioni su come eseguire l'override dell'ordine.</span><span class="sxs-lookup"><span data-stu-id="c1955-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="c1955-195">Vedere [Annullamento e blocco](xref:mvc/controllers/filters#cancellation-and-short-circuiting) per istruzioni su come bloccare la pipeline filtro da un filtro.</span><span class="sxs-lookup"><span data-stu-id="c1955-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="c1955-196">Autorizzare l'attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="c1955-196">Authorize filter attribute</span></span>

<span data-ttu-id="c1955-197">L'attributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) può essere applicato a `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="c1955-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
