---
title: 'Metodi di filtro per le :::no-loc(Razor)::: pagine in ASP.NET Core'
author: Rick-Anderson
description: 'Informazioni su come creare metodi di filtro per le :::no-loc(Razor)::: pagine in ASP.NET Core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: razor-pages/filter
ms.openlocfilehash: a6d25c1b88e09560c1aad9aefd9148f7fe293909
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056829"
---
# <a name="filter-methods-for-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="5046c-103">Metodi di filtro per le :::no-loc(Razor)::: pagine in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5046c-103">Filter methods for :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5046c-104">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5046c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5046c-105">:::no-loc(Razor)::: I filtri di pagina [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) consentono :::no-loc(Razor)::: alle pagine di eseguire codice prima e dopo l'esecuzione di un :::no-loc(Razor)::: gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-105">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="5046c-106">:::no-loc(Razor)::: I filtri di pagina sono simili a [ASP.NET Core filtri azione MVC](xref:mvc/controllers/filters#action-filters), ma non possono essere applicati ai singoli metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-106">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="5046c-107">:::no-loc(Razor)::: Filtri di pagina:</span><span class="sxs-lookup"><span data-stu-id="5046c-107">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="5046c-108">Eseguono il codice dopo aver selezionato un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="5046c-109">Eseguono il codice prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="5046c-110">Eseguono il codice dopo l'esecuzione del metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="5046c-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="5046c-111">Possono essere implementati in una pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="5046c-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="5046c-112">Non possono essere applicati a metodi del gestore pagina specifici.</span><span class="sxs-lookup"><span data-stu-id="5046c-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="5046c-113">Le dipendenze del costruttore possono essere popolate dall' [inserimento di dipendenze](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="5046c-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="5046c-114">Per ulteriori informazioni, vedere [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) e [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span><span class="sxs-lookup"><span data-stu-id="5046c-114">For more information, see [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) and [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute).</span></span>

<span data-ttu-id="5046c-115">Mentre i costruttori di pagina e il middleware abilitano l'esecuzione di codice personalizzato prima dell'esecuzione di un metodo del gestore, solo :::no-loc(Razor)::: i filtri di pagina consentono l'accesso a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> e alla pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-115">While page constructors and middleware enable executing custom code before a handler method executes, only :::no-loc(Razor)::: Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="5046c-116">Il middleware ha accesso a `HttpContext` , ma non al "contesto di pagina".</span><span class="sxs-lookup"><span data-stu-id="5046c-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="5046c-117">I filtri hanno un <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> parametro derivato, che fornisce l'accesso a `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="5046c-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="5046c-118">Di seguito è riportato un esempio per un filtro di pagina: [implementare un attributo di filtro](#ifa) che aggiunge un'intestazione alla risposta, operazione che non può essere eseguita con i costruttori o il middleware.</span><span class="sxs-lookup"><span data-stu-id="5046c-118">Here's a sample for a page filter: [Implement a filter attribute](#ifa) that adds a header to the response, something that can't be done with constructors or middleware.</span></span> <span data-ttu-id="5046c-119">L'accesso al contesto della pagina, che include l'accesso alle istanze della pagina e del relativo modello, è disponibile solo quando si eseguono filtri, gestori o il corpo di una :::no-loc(Razor)::: pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-119">Access to the page context, which includes access to the instances of the page and it's model, are only available when executing filters, handlers, or the body of a :::no-loc(Razor)::: Page.</span></span>

<span data-ttu-id="5046c-120">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5046c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5046c-121">:::no-loc(Razor)::: I filtri di pagina forniscono i metodi seguenti, che possono essere applicati a livello globale o a livello di pagina:</span><span class="sxs-lookup"><span data-stu-id="5046c-121">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="5046c-122">Metodi sincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-122">Synchronous methods:</span></span>

  * <span data-ttu-id="5046c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): metodo chiamato dopo la selezione di un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-123">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5046c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): metodo chiamato prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-124">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="5046c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): metodo chiamato dopo che il metodo del gestore è stato eseguito e prima del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="5046c-125">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="5046c-126">Metodi asincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-126">Asynchronous methods:</span></span>

  * <span data-ttu-id="5046c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono dopo la selezione del metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-127">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5046c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono prima che venga richiamato il metodo del gestore, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-128">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="5046c-129">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="5046c-129">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="5046c-130">Il framework controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="5046c-130">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="5046c-131">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="5046c-131">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="5046c-132">Se entrambe le interfacce sono implementate, verranno chiamati solo i metodi asincroni.</span><span class="sxs-lookup"><span data-stu-id="5046c-132">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="5046c-133">La stessa regola vale per gli override nelle pagine. Implementare la versione sincrona o la versione asincrona dell'override, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="5046c-133">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="5046c-134">Implementare i :::no-loc(Razor)::: filtri di pagina a livello globale</span><span class="sxs-lookup"><span data-stu-id="5046c-134">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="5046c-135">Il codice seguente implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5046c-135">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="5046c-136">Nel codice precedente, `ProcessUserAgent.Write` è il codice fornito dall'utente che funziona con la stringa agente utente.</span><span class="sxs-lookup"><span data-stu-id="5046c-136">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="5046c-137">Il codice seguente abilita `SampleAsyncPageFilter` nella classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5046c-137">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="5046c-138">Il codice seguente chiama <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> per applicare l'oggetto `SampleAsyncPageFilter` solo alle pagine in */Movies* :</span><span class="sxs-lookup"><span data-stu-id="5046c-138">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies* :</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="5046c-139">Il codice seguente implementa il filtro `IPageFilter` sincrono:</span><span class="sxs-lookup"><span data-stu-id="5046c-139">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="5046c-140">Il codice seguente abilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5046c-140">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="5046c-141">Implementare i :::no-loc(Razor)::: filtri di pagina eseguendo l'override dei metodi di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-141">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="5046c-142">Il codice seguente esegue l'override dei :::no-loc(Razor)::: filtri di pagina asincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-142">The following code overrides the asynchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="5046c-143">Implementare un attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-143">Implement a filter attribute</span></span>

<span data-ttu-id="5046c-144">Il filtro di filtro basato su attributi predefinito <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> può essere sottoclassato.</span><span class="sxs-lookup"><span data-stu-id="5046c-144">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="5046c-145">Il filtro seguente aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="5046c-145">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="5046c-146">Il codice seguente applica l'attributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="5046c-146">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="5046c-147">Usare uno strumento come gli strumenti di sviluppo del browser per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="5046c-147">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="5046c-148">In **intestazioni risposta** `author: Rick` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="5046c-148">Under **Response Headers** , `author: Rick` is displayed.</span></span>

<span data-ttu-id="5046c-149">Vedere [Override dell'ordine predefinito](xref:mvc/controllers/filters#overriding-the-default-order) per istruzioni su come eseguire l'override dell'ordine.</span><span class="sxs-lookup"><span data-stu-id="5046c-149">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="5046c-150">Vedere [Annullamento e blocco](xref:mvc/controllers/filters#cancellation-and-short-circuiting) per istruzioni su come bloccare la pipeline filtro da un filtro.</span><span class="sxs-lookup"><span data-stu-id="5046c-150">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="5046c-151">Autorizzare l'attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-151">Authorize filter attribute</span></span>

<span data-ttu-id="5046c-152">L'attributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) può essere applicato a `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="5046c-152">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5046c-153">Autore: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5046c-153">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5046c-154">:::no-loc(Razor)::: I filtri di pagina [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) e [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) consentono :::no-loc(Razor)::: alle pagine di eseguire codice prima e dopo l'esecuzione di un :::no-loc(Razor)::: gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-154">:::no-loc(Razor)::: Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow :::no-loc(Razor)::: Pages to run code before and after a :::no-loc(Razor)::: Page handler is run.</span></span> <span data-ttu-id="5046c-155">:::no-loc(Razor)::: I filtri di pagina sono simili a [ASP.NET Core filtri azione MVC](xref:mvc/controllers/filters#action-filters), ma non possono essere applicati ai singoli metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="5046c-155">:::no-loc(Razor)::: Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

<span data-ttu-id="5046c-156">:::no-loc(Razor)::: Filtri di pagina:</span><span class="sxs-lookup"><span data-stu-id="5046c-156">:::no-loc(Razor)::: Page filters:</span></span>

* <span data-ttu-id="5046c-157">Eseguono il codice dopo aver selezionato un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-157">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="5046c-158">Eseguono il codice prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-158">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="5046c-159">Eseguono il codice dopo l'esecuzione del metodo del gestore.</span><span class="sxs-lookup"><span data-stu-id="5046c-159">Run code after the handler method executes.</span></span>
* <span data-ttu-id="5046c-160">Possono essere implementati in una pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="5046c-160">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="5046c-161">Non possono essere applicati a metodi del gestore pagina specifici.</span><span class="sxs-lookup"><span data-stu-id="5046c-161">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="5046c-162">Il codice può essere eseguito prima dell'esecuzione di un metodo del gestore usando il costruttore o il middleware della pagina, ma solo i :::no-loc(Razor)::: filtri di pagina hanno accesso a [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span><span class="sxs-lookup"><span data-stu-id="5046c-162">Code can be run before a handler method executes using the page constructor or middleware, but only :::no-loc(Razor)::: Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_:::no-loc(Razor):::Pages_PageModel_HttpContext).</span></span> <span data-ttu-id="5046c-163">I filtri contengono un parametro derivato da [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0), che fornisce accesso a `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5046c-163">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="5046c-164">Ad esempio, nell'esempio [Implementare un attributo di filtro](#ifa) viene aggiunta un'intestazione alla risposta, operazione impossibile con costruttori o middleware.</span><span class="sxs-lookup"><span data-stu-id="5046c-164">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="5046c-165">[Visualizzare o scaricare il codice di esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([procedura per il download](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5046c-165">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5046c-166">:::no-loc(Razor)::: I filtri di pagina forniscono i metodi seguenti, che possono essere applicati a livello globale o a livello di pagina:</span><span class="sxs-lookup"><span data-stu-id="5046c-166">:::no-loc(Razor)::: Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="5046c-167">Metodi sincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-167">Synchronous methods:</span></span>

  * <span data-ttu-id="5046c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0): metodo chiamato dopo la selezione di un metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-168">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5046c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0): metodo chiamato prima che il metodo del gestore venga eseguito, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-169">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="5046c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0): metodo chiamato dopo che il metodo del gestore è stato eseguito e prima del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="5046c-170">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="5046c-171">Metodi asincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-171">Asynchronous methods:</span></span>

  * <span data-ttu-id="5046c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono dopo la selezione del metodo del gestore, ma prima che si verifichi l'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-172">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5046c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0): metodo chiamato in modo asincrono prima che venga richiamato il metodo del gestore, al termine dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="5046c-173">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="5046c-174">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="5046c-174">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="5046c-175">Il framework controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="5046c-175">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="5046c-176">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="5046c-176">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="5046c-177">Se entrambe le interfacce sono implementate, verranno chiamati solo i metodi asincroni.</span><span class="sxs-lookup"><span data-stu-id="5046c-177">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="5046c-178">La stessa regola vale per gli override nelle pagine. Implementare la versione sincrona o la versione asincrona dell'override, non entrambe.</span><span class="sxs-lookup"><span data-stu-id="5046c-178">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-no-locrazor-page-filters-globally"></a><span data-ttu-id="5046c-179">Implementare i :::no-loc(Razor)::: filtri di pagina a livello globale</span><span class="sxs-lookup"><span data-stu-id="5046c-179">Implement :::no-loc(Razor)::: Page filters globally</span></span>

<span data-ttu-id="5046c-180">Il codice seguente implementa `IAsyncPageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5046c-180">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="5046c-181">Nel codice precedente, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) non è obbligatorio.</span><span class="sxs-lookup"><span data-stu-id="5046c-181">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="5046c-182">Nell'esempio viene utilizzato per fornire informazioni di traccia per l'applicazione.</span><span class="sxs-lookup"><span data-stu-id="5046c-182">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="5046c-183">Il codice seguente abilita `SampleAsyncPageFilter` nella classe `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5046c-183">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="5046c-184">Il codice seguente mostra la classe `Startup` completa:</span><span class="sxs-lookup"><span data-stu-id="5046c-184">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="5046c-185">Il codice seguente chiama `AddFolderApplicationModelConvention` per applicare `SampleAsyncPageFilter` solo alle pagine in */subFolder* :</span><span class="sxs-lookup"><span data-stu-id="5046c-185">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder* :</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="5046c-186">Il codice seguente implementa il filtro `IPageFilter` sincrono:</span><span class="sxs-lookup"><span data-stu-id="5046c-186">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="5046c-187">Il codice seguente abilita `SamplePageFilter`:</span><span class="sxs-lookup"><span data-stu-id="5046c-187">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-no-locrazor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="5046c-188">Implementare i :::no-loc(Razor)::: filtri di pagina eseguendo l'override dei metodi di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-188">Implement :::no-loc(Razor)::: Page filters by overriding filter methods</span></span>

<span data-ttu-id="5046c-189">Il codice seguente esegue l'override dei :::no-loc(Razor)::: filtri di pagina sincroni:</span><span class="sxs-lookup"><span data-stu-id="5046c-189">The following code overrides the synchronous :::no-loc(Razor)::: Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="5046c-190">Implementare un attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-190">Implement a filter attribute</span></span>

<span data-ttu-id="5046c-191">Il filtro basato su attributi predefinito [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) può essere sottoclassato.</span><span class="sxs-lookup"><span data-stu-id="5046c-191">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="5046c-192">Il filtro seguente aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="5046c-192">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="5046c-193">Il codice seguente applica l'attributo `AddHeader`:</span><span class="sxs-lookup"><span data-stu-id="5046c-193">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="5046c-194">Vedere [Override dell'ordine predefinito](xref:mvc/controllers/filters#overriding-the-default-order) per istruzioni su come eseguire l'override dell'ordine.</span><span class="sxs-lookup"><span data-stu-id="5046c-194">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="5046c-195">Vedere [Annullamento e blocco](xref:mvc/controllers/filters#cancellation-and-short-circuiting) per istruzioni su come bloccare la pipeline filtro da un filtro.</span><span class="sxs-lookup"><span data-stu-id="5046c-195">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="5046c-196">Autorizzare l'attributo di filtro</span><span class="sxs-lookup"><span data-stu-id="5046c-196">Authorize filter attribute</span></span>

<span data-ttu-id="5046c-197">L'attributo [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) può essere applicato a `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="5046c-197">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end