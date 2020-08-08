---
title: Filtri in ASP.NET Core
author: Rick-Anderson
description: Informazioni sul funzionamento dei filtri e su come usarli in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: 11d0c514dd15e787224510991ffb81680c9fc479
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019342"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="8c117-103">Filtri in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c117-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c117-104">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8c117-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8c117-105">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8c117-106">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8c117-107">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="8c117-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8c117-108">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="8c117-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8c117-109">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="8c117-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8c117-110">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8c117-111">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="8c117-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="8c117-112">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="8c117-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8c117-113">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="8c117-114">I filtri non funzionano direttamente con i [ Razor componenti](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="8c117-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="8c117-115">Un filtro può influire indirettamente su un componente solo quando:</span><span class="sxs-lookup"><span data-stu-id="8c117-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="8c117-116">Il componente è incorporato in una pagina o in una vista.</span><span class="sxs-lookup"><span data-stu-id="8c117-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="8c117-117">La pagina o il controller/Visualizzazione usa il filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="8c117-118">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8c117-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8c117-119">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="8c117-119">How filters work</span></span>

<span data-ttu-id="8c117-120">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="8c117-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="8c117-121">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="8c117-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altri middleware, middleware di routing, selezione dell'azione e pipeline di chiamata dell'azione.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8c117-124">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-124">Filter types</span></span>

<span data-ttu-id="8c117-125">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="8c117-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8c117-126">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8c117-127">I filtri di autorizzazione consentono di cortocircuitare la pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="8c117-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="8c117-128">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8c117-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8c117-129">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-129">Run after authorization.</span></span>  
  * <span data-ttu-id="8c117-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>esegue il codice prima del resto della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8c117-131">Ad esempio, `OnResourceExecuting` esegue il codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8c117-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="8c117-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>esegue il codice una volta completato il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8c117-133">[Filtri azione](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="8c117-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="8c117-134">Eseguire il codice immediatamente prima e dopo la chiamata di un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="8c117-135">Consente di modificare gli argomenti passati in un'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="8c117-136">Può modificare il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="8c117-137">**Non** sono supportate nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="8c117-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8c117-138">I [filtri eccezioni](#exception-filters) applicano i criteri globali alle eccezioni non gestite che si verificano prima della scrittura del corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="8c117-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="8c117-139">I [filtri risultati](#result-filters) eseguono il codice immediatamente prima e dopo l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="8c117-140">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="8c117-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8c117-141">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="8c117-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8c117-142">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8c117-145">Implementazione</span><span class="sxs-lookup"><span data-stu-id="8c117-145">Implementation</span></span>

<span data-ttu-id="8c117-146">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="8c117-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8c117-147">I filtri sincroni eseguono codice prima e dopo la fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="8c117-148">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*>, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="8c117-149">La chiamata di <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c117-150">Nel codice precedente, il [debug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) è una funzione di utilità nell' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="8c117-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="8c117-151">I filtri asincroni definiscono un `On-Stage-ExecutionAsync` metodo.</span><span class="sxs-lookup"><span data-stu-id="8c117-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="8c117-152">Ad esempio, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="8c117-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8c117-153">Nel codice precedente, `SampleAsyncActionFilter` ha un <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8c117-154">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="8c117-154">Multiple filter stages</span></span>

<span data-ttu-id="8c117-155">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="8c117-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8c117-156">Ad esempio, la <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> classe implementa:</span><span class="sxs-lookup"><span data-stu-id="8c117-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="8c117-157">Sincrona: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="8c117-158">Asincrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="8c117-159">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="8c117-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8c117-160">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="8c117-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8c117-161">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="8c117-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8c117-162">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="8c117-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8c117-163">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8c117-164">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="8c117-164">Built-in filter attributes</span></span>

<span data-ttu-id="8c117-165">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="8c117-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8c117-166">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="8c117-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-167">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="8c117-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8c117-168">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="8c117-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="8c117-169">Usare uno strumento come gli [strumenti di sviluppo del browser](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) per esaminare le intestazioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="8c117-170">In **intestazioni risposta** `author: Rick Anderson` viene visualizzato.</span><span class="sxs-lookup"><span data-stu-id="8c117-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="8c117-171">Il codice seguente implementa un oggetto `ActionFilterAttribute` che:</span><span class="sxs-lookup"><span data-stu-id="8c117-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="8c117-172">Legge il titolo e il nome dal sistema di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="8c117-173">A differenza dell'esempio precedente, il codice seguente non richiede l'aggiunta di parametri di filtro al codice.</span><span class="sxs-lookup"><span data-stu-id="8c117-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="8c117-174">Aggiunge il titolo e il nome all'intestazione della risposta.</span><span class="sxs-lookup"><span data-stu-id="8c117-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-175">Le opzioni di configurazione vengono fornite dal [sistema di configurazione](xref:fundamentals/configuration/index) usando il [modello di opzioni](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="8c117-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8c117-176">Ad esempio, dal *appsettings.jssu* file:</span><span class="sxs-lookup"><span data-stu-id="8c117-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="8c117-177">Nel `StartUp.ConfigureServices` eseguire le operazioni seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="8c117-178">La `PositionOptions` classe viene aggiunta al contenitore del servizio con l' `"Position"` area di configurazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="8c117-179">`MyActionFilterAttribute`Viene aggiunto al contenitore del servizio.</span><span class="sxs-lookup"><span data-stu-id="8c117-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="8c117-180">Il codice seguente illustra la `PositionOptions` classe:</span><span class="sxs-lookup"><span data-stu-id="8c117-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8c117-181">Il codice seguente applica al `MyActionFilterAttribute` `Index2` Metodo:</span><span class="sxs-lookup"><span data-stu-id="8c117-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="8c117-182">In **intestazioni di risposta**, `author: Rick Anderson` e `Editor: Joe Smith` viene visualizzato quando `Sample/Index2` viene chiamato l'endpoint.</span><span class="sxs-lookup"><span data-stu-id="8c117-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="8c117-183">Il codice seguente applica `MyActionFilterAttribute` e alla `AddHeaderAttribute` Razor pagina:</span><span class="sxs-lookup"><span data-stu-id="8c117-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8c117-184">Non è possibile applicare i filtri ai Razor metodi del gestore di pagina.</span><span class="sxs-lookup"><span data-stu-id="8c117-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="8c117-185">Possono essere applicati al Razor modello di pagina o a livello globale.</span><span class="sxs-lookup"><span data-stu-id="8c117-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="8c117-186">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="8c117-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8c117-187">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="8c117-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8c117-188">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="8c117-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="8c117-189">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="8c117-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8c117-190">Uso di un attributo in un'azione del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="8c117-191">Gli attributi di filtro non possono essere applicati ai Razor metodi del gestore di pagine.</span><span class="sxs-lookup"><span data-stu-id="8c117-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="8c117-192">Uso di un attributo in un controller o in una Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="8c117-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="8c117-193">A livello globale per tutti i controller, le azioni e le Razor pagine, come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="8c117-194">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="8c117-194">Default order of execution</span></span>

<span data-ttu-id="8c117-195">Quando sono presenti più filtri per una particolare fase della pipeline, l'ambito determina l'ordine di esecuzione predefinito dei filtri.</span><span class="sxs-lookup"><span data-stu-id="8c117-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8c117-196">I filtri globali racchiudono i filtri di classe, che a loro volta racchiudono i filtri dei metodi.</span><span class="sxs-lookup"><span data-stu-id="8c117-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="8c117-197">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="8c117-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8c117-198">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="8c117-198">The filter sequence:</span></span>

* <span data-ttu-id="8c117-199">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="8c117-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8c117-200">Codice *prima* dei filtri di pagina e controller Razor .</span><span class="sxs-lookup"><span data-stu-id="8c117-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="8c117-201">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8c117-202">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8c117-203">Codice *dopo* i filtri del controller e della Razor pagina.</span><span class="sxs-lookup"><span data-stu-id="8c117-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="8c117-204">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="8c117-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8c117-205">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="8c117-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8c117-206">Sequenza</span><span class="sxs-lookup"><span data-stu-id="8c117-206">Sequence</span></span> | <span data-ttu-id="8c117-207">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-207">Filter scope</span></span> | <span data-ttu-id="8c117-208">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8c117-209">1</span><span class="sxs-lookup"><span data-stu-id="8c117-209">1</span></span> | <span data-ttu-id="8c117-210">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-211">2</span><span class="sxs-lookup"><span data-stu-id="8c117-211">2</span></span> | <span data-ttu-id="8c117-212">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="8c117-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="8c117-213">3</span><span class="sxs-lookup"><span data-stu-id="8c117-213">3</span></span> | <span data-ttu-id="8c117-214">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-215">4</span><span class="sxs-lookup"><span data-stu-id="8c117-215">4</span></span> | <span data-ttu-id="8c117-216">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c117-217">5</span><span class="sxs-lookup"><span data-stu-id="8c117-217">5</span></span> | <span data-ttu-id="8c117-218">Controller o Razor pagina</span><span class="sxs-lookup"><span data-stu-id="8c117-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c117-219">6</span><span class="sxs-lookup"><span data-stu-id="8c117-219">6</span></span> | <span data-ttu-id="8c117-220">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="8c117-221">Filtri a livello di controller</span><span class="sxs-lookup"><span data-stu-id="8c117-221">Controller level filters</span></span>

<span data-ttu-id="8c117-222">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8c117-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8c117-223">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="8c117-223">These methods:</span></span>

* <span data-ttu-id="8c117-224">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8c117-225">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8c117-226">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8c117-227">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8c117-228">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8c117-229">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="8c117-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8c117-230">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8c117-230">The `TestController`:</span></span>

* <span data-ttu-id="8c117-231">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8c117-232">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8c117-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="8c117-233">Passando a `https://localhost:5001/Test2/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8c117-234">Filtri a livello di controller impostare la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="8c117-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="8c117-235">I filtri a livello di controller **non** possono essere impostati per l'esecuzione dopo i filtri applicati ai metodi.</span><span class="sxs-lookup"><span data-stu-id="8c117-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="8c117-236">L'ordine è illustrato nella sezione successiva.</span><span class="sxs-lookup"><span data-stu-id="8c117-236">Order is explained in the next section.</span></span>

<span data-ttu-id="8c117-237">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8c117-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8c117-238">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="8c117-238">Overriding the default order</span></span>

<span data-ttu-id="8c117-239">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8c117-240">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8c117-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8c117-241">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="8c117-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8c117-242">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="8c117-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8c117-243">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="8c117-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8c117-244">La `Order` proprietà viene impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="8c117-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="8c117-245">Considerare i due filtri azione nel controller seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8c117-246">In è stato aggiunto un filtro globale `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8c117-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="8c117-247">I 3 filtri vengono eseguiti nell'ordine seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="8c117-248">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="8c117-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8c117-249">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="8c117-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8c117-250">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="8c117-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8c117-251">Come indicato in precedenza, i filtri a livello di controller impostano la proprietà [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) su `int.MinValue` per i filtri incorporati, l'ambito determina l'ordine a meno che non `Order` sia impostato su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="8c117-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="8c117-252">Nel codice precedente, `MySampleActionFilter` ha ambito globale, quindi viene eseguito prima di `MyAction2FilterAttribute` , che ha ambito del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="8c117-253">Per `MyAction2FilterAttribute` eseguire prima l'esecuzione, impostare l'ordine su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="8c117-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="8c117-254">Per eseguire prima l'esecuzione del filtro globale `MySampleActionFilter` , impostare `Order` su `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="8c117-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8c117-255">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="8c117-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8c117-256">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8c117-257">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="8c117-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-258">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8c117-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8c117-259">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8c117-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8c117-260">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8c117-261">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8c117-262">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8c117-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8c117-263">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="8c117-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8c117-264">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="8c117-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="8c117-265">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8c117-265">Dependency injection</span></span>

<span data-ttu-id="8c117-266">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="8c117-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8c117-267">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8c117-268">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="8c117-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8c117-269">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="8c117-269">A type-activated filter means:</span></span>

* <span data-ttu-id="8c117-270">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-270">An instance is created for each request.</span></span>
* <span data-ttu-id="8c117-271">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c117-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8c117-272">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c117-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8c117-273">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="8c117-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8c117-274">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="8c117-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8c117-275">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="8c117-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8c117-276">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8c117-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="8c117-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8c117-278">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8c117-279">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-279">Loggers are available from DI.</span></span> <span data-ttu-id="8c117-280">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8c117-281">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8c117-282">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="8c117-282">Logging added to filters:</span></span>

* <span data-ttu-id="8c117-283">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8c117-284">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="8c117-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8c117-285">Le azioni di log dei filtri incorporati e gli eventi del Framework.</span><span class="sxs-lookup"><span data-stu-id="8c117-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8c117-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c117-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="8c117-287">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8c117-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8c117-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8c117-289">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8c117-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c117-290">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="8c117-291">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8c117-292">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8c117-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8c117-293">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="8c117-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c117-294">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="8c117-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8c117-295">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8c117-296">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8c117-297">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="8c117-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8c117-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8c117-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c117-299">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c117-300">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8c117-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c117-301">TypeFilterAttribute</span></span>

<span data-ttu-id="8c117-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8c117-303">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="8c117-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8c117-304">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8c117-305">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8c117-306">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8c117-307">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="8c117-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8c117-308">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8c117-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8c117-309">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="8c117-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c117-310">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8c117-311">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="8c117-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8c117-312">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="8c117-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8c117-313">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="8c117-313">Authorization filters</span></span>

<span data-ttu-id="8c117-314">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="8c117-314">Authorization filters:</span></span>

* <span data-ttu-id="8c117-315">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8c117-316">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-316">Control access to action methods.</span></span>
* <span data-ttu-id="8c117-317">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="8c117-318">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="8c117-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8c117-319">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="8c117-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8c117-320">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="8c117-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="8c117-321">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-321">Calls the authorization system.</span></span>
* <span data-ttu-id="8c117-322">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="8c117-322">Does not authorize requests.</span></span>

<span data-ttu-id="8c117-323">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="8c117-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8c117-324">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="8c117-324">The exception will not be handled.</span></span>
* <span data-ttu-id="8c117-325">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8c117-326">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8c117-327">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="8c117-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8c117-328">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="8c117-328">Resource filters</span></span>

<span data-ttu-id="8c117-329">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="8c117-329">Resource filters:</span></span>

* <span data-ttu-id="8c117-330">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8c117-331">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8c117-332">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="8c117-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8c117-333">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8c117-334">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="8c117-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8c117-335">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="8c117-335">Resource filter examples:</span></span>

* <span data-ttu-id="8c117-336">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="8c117-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8c117-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8c117-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8c117-338">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="8c117-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8c117-339">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="8c117-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8c117-340">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="8c117-340">Action filters</span></span>

<span data-ttu-id="8c117-341">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="8c117-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8c117-342">RazorPages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8c117-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8c117-343">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8c117-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8c117-344">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-344">Action filters:</span></span>

* <span data-ttu-id="8c117-345">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8c117-346">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8c117-347">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c117-348">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8c117-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input in un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="8c117-350"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8c117-351"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8c117-352">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8c117-353">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8c117-354">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c117-355">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8c117-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c117-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8c117-358">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="8c117-358">Setting this property to null:</span></span>

  * <span data-ttu-id="8c117-359">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="8c117-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8c117-360">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8c117-361">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="8c117-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8c117-362">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8c117-363">Restituisca il valore `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8c117-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8c117-364">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="8c117-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8c117-365">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="8c117-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8c117-366">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="8c117-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8c117-367">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="8c117-367">Validate model state.</span></span>
* <span data-ttu-id="8c117-368">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="8c117-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="8c117-369">I controller annotati con l' `[ApiController]` attributo convalidano automaticamente lo stato del modello e restituiscono una risposta 400.</span><span class="sxs-lookup"><span data-stu-id="8c117-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="8c117-370">Per altre informazioni, vedere [Risposte HTTP 400 automatiche](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="8c117-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="8c117-371">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8c117-372">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="8c117-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8c117-373">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c117-374">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8c117-375">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="8c117-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8c117-376">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="8c117-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8c117-377">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8c117-378">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="8c117-378">Exception filters</span></span>

<span data-ttu-id="8c117-379">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-379">Exception filters:</span></span>

* <span data-ttu-id="8c117-380">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="8c117-381">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="8c117-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8c117-382">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="8c117-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8c117-383">Il codice seguente verifica il filtro eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="8c117-384">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-384">Exception filters:</span></span>

* <span data-ttu-id="8c117-385">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-385">Don't have before and after events.</span></span>
* <span data-ttu-id="8c117-386">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8c117-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8c117-387">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8c117-388">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="8c117-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8c117-389">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="8c117-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8c117-390">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-390">This stops propagation of the exception.</span></span> <span data-ttu-id="8c117-391">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="8c117-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8c117-392">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-392">Only an action filter can do that.</span></span>

<span data-ttu-id="8c117-393">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-393">Exception filters:</span></span>

* <span data-ttu-id="8c117-394">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8c117-395">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="8c117-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8c117-396">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8c117-397">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="8c117-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8c117-398">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="8c117-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8c117-399">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="8c117-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8c117-400">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="8c117-400">Result filters</span></span>

<span data-ttu-id="8c117-401">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="8c117-401">Result filters:</span></span>

* <span data-ttu-id="8c117-402">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="8c117-402">Implement an interface:</span></span>
  * <span data-ttu-id="8c117-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8c117-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8c117-405">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8c117-406">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c117-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8c117-407">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="8c117-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c117-408">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8c117-409">Un'azione che restituisce una visualizzazione include tutte le operazioni di elaborazione Razor come parte dell' <xref:Microsoft.AspNetCore.Mvc.ViewResult> oggetto in esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8c117-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8c117-410">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="8c117-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8c117-411">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="8c117-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8c117-412">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8c117-413">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="8c117-414">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8c117-415">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8c117-416">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="8c117-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8c117-417">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="8c117-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8c117-418">Generazione di un'eccezione in `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="8c117-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="8c117-419">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8c117-420">Viene considerato un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c117-421">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="8c117-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="8c117-422">Se la risposta è già stata inviata al client, non è possibile modificarla.</span><span class="sxs-lookup"><span data-stu-id="8c117-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="8c117-423">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8c117-424">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8c117-425">`Exception`L'impostazione di su null consente di gestire in modo efficace un'eccezione e impedisce che l'eccezione venga generata nuovamente in un secondo momento nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="8c117-426">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="8c117-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8c117-427">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="8c117-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8c117-428">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8c117-429">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="8c117-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8c117-430">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="8c117-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8c117-431">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="8c117-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8c117-432">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c117-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8c117-433">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8c117-434">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="8c117-434">This includes action results produced by:</span></span>

* <span data-ttu-id="8c117-435">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="8c117-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8c117-436">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-436">Exception filters.</span></span>

<span data-ttu-id="8c117-437">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="8c117-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8c117-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c117-438">IFilterFactory</span></span>

<span data-ttu-id="8c117-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8c117-440">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8c117-441">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="8c117-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8c117-442">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="8c117-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8c117-443">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8c117-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8c117-444">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="8c117-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8c117-445">Il filtro viene applicato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="8c117-446">Testare il codice precedente eseguendo l' [esempio di download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="8c117-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="8c117-447">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="8c117-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8c117-448">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8c117-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8c117-449">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8c117-450">**Autore:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="8c117-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="8c117-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8c117-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8c117-452">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8c117-452">**internal:** `My header`</span></span>

<span data-ttu-id="8c117-453">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="8c117-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8c117-454">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="8c117-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8c117-455">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="8c117-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8c117-456">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="8c117-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="8c117-457">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8c117-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8c117-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c117-459">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c117-460">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8c117-461">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="8c117-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8c117-462">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="8c117-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8c117-463">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8c117-464">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8c117-465">Tuttavia, i filtri differiscono dal middleware in quanto fanno parte del runtime, il che significa che hanno accesso a contesto e costrutti.</span><span class="sxs-lookup"><span data-stu-id="8c117-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="8c117-466">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8c117-467">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="8c117-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8c117-468">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="8c117-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8c117-469">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8c117-470">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="8c117-470">Next actions</span></span>

* <span data-ttu-id="8c117-471">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8c117-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8c117-472">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="8c117-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c117-473">Di [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8c117-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8c117-474">I *filtri* in ASP.NET Core consentono l'esecuzione del codice prima o dopo fasi specifiche della pipeline di elaborazione della richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8c117-475">I filtri predefiniti gestiscono attività, ad esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8c117-476">Autorizzazione (impedire l'accesso alle risorse per cui un utente non è autorizzato).</span><span class="sxs-lookup"><span data-stu-id="8c117-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8c117-477">Memorizzazione nella cache delle risposte (blocco della pipeline delle richieste per restituire una risposta memorizzata nella cache).</span><span class="sxs-lookup"><span data-stu-id="8c117-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8c117-478">I filtri personalizzati possono essere creati per gestire problemi relativi a più settori.</span><span class="sxs-lookup"><span data-stu-id="8c117-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8c117-479">Esempi di problematiche trasversali includono la gestione degli errori, la memorizzazione nella cache, la configurazione, l'autorizzazione e la registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8c117-480">I filtri evitano la duplicazione del codice.</span><span class="sxs-lookup"><span data-stu-id="8c117-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="8c117-481">Ad esempio, un filtro eccezioni per la gestione degli errori potrebbe consolidare la gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="8c117-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8c117-482">Questo documento si applica a Razor pagine, controller API e controller con visualizzazioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="8c117-483">[Visualizzare o scaricare un esempio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([procedura per il download](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8c117-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8c117-484">Funzionamento dei filtri</span><span class="sxs-lookup"><span data-stu-id="8c117-484">How filters work</span></span>

<span data-ttu-id="8c117-485">I filtri vengono eseguiti all'interno della *pipeline di chiamata di azioni ASP.NET Core*, talvolta definita *pipeline di filtro*.</span><span class="sxs-lookup"><span data-stu-id="8c117-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="8c117-486">La pipeline di filtro viene eseguita dopo che ASP.NET Core ha selezionato l'azione da eseguire.</span><span class="sxs-lookup"><span data-stu-id="8c117-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![La richiesta viene elaborata tramite altro middleware, il middleware di routing, la selezione dell'azione e la pipeline di chiamata di azioni ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8c117-489">Tipi di filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-489">Filter types</span></span>

<span data-ttu-id="8c117-490">Ogni tipo di filtro viene eseguito in una fase diversa della pipeline di filtro:</span><span class="sxs-lookup"><span data-stu-id="8c117-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8c117-491">I [filtri di autorizzazione](#authorization-filters) vengono eseguiti per primi e consentono di determinare se l'utente è autorizzato per la richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8c117-492">I filtri di autorizzazione causano il corto circuito della pipeline se la richiesta non è autorizzata.</span><span class="sxs-lookup"><span data-stu-id="8c117-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="8c117-493">[Filtri risorse](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="8c117-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8c117-494">Vengono eseguiti dopo l'autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-494">Run after authorization.</span></span>  
  * <span data-ttu-id="8c117-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> può eseguire codice prima del resto della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8c117-496">Ad esempio, `OnResourceExecuting` può eseguire codice prima dell'associazione di modelli.</span><span class="sxs-lookup"><span data-stu-id="8c117-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="8c117-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> può eseguire codice dopo che il resto della pipeline è stato completato.</span><span class="sxs-lookup"><span data-stu-id="8c117-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8c117-498">I [filtri azione](#action-filters) possono eseguire codice immediatamente prima e dopo la chiamata di un metodo di azione singolo.</span><span class="sxs-lookup"><span data-stu-id="8c117-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="8c117-499">Possono essere usati per modificare gli argomenti passati a un'azione e il risultato restituito dall'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="8c117-500">I filtri azione **non** sono supportati nelle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="8c117-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8c117-501">I [filtri eccezioni](#exception-filters) vengono usati per applicare i criteri globali a eccezioni non gestite che si verificano prima che qualsiasi elemento sia stato scritto nel corpo della risposta.</span><span class="sxs-lookup"><span data-stu-id="8c117-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="8c117-502">I [filtri risultato](#result-filters) possono eseguire codice immediatamente prima e dopo l'esecuzione di risultati di azioni singole.</span><span class="sxs-lookup"><span data-stu-id="8c117-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="8c117-503">Vengono eseguiti solo quando il metodo di azione è stato eseguito correttamente.</span><span class="sxs-lookup"><span data-stu-id="8c117-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8c117-504">Sono utili per la logica che deve racchiudere l'esecuzione del formattatore o di una vista.</span><span class="sxs-lookup"><span data-stu-id="8c117-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8c117-505">Il diagramma seguente illustra come interagiscono i tipi di filtro nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![La richiesta passa attraverso i filtri autorizzazione, i filtri risorse, l'associazione di modelli, i filtri azione, l'esecuzione dell'azione e la conversione del risultato dell'azione, i filtri eccezioni, i filtri risultato e l'esecuzione del risultato.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8c117-508">Implementazione</span><span class="sxs-lookup"><span data-stu-id="8c117-508">Implementation</span></span>

<span data-ttu-id="8c117-509">I filtri supportano sia implementazioni sincrone che asincrone tramite definizioni di interfaccia diverse.</span><span class="sxs-lookup"><span data-stu-id="8c117-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8c117-510">I filtri sincroni possono eseguire codice prima (`On-Stage-Executing`) e dopo (`On-Stage-Executed`) la relativa fase della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="8c117-511">La chiamata di `OnActionExecuting`, ad esempio, avviene prima della chiamata del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="8c117-512">La chiamata di `OnActionExecuted` avviene dopo l'esecuzione del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c117-513">I filtri asincroni definiscono un metodo `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="8c117-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8c117-514">Nel codice precedente `SampleAsyncActionFilter` ha un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) che esegue il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="8c117-515">Ognuno dei metodi `On-Stage-ExecutionAsync` accetta un oggetto `FilterType-ExecutionDelegate` che esegue la fase della pipeline del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8c117-516">Fasi di filtro multiple</span><span class="sxs-lookup"><span data-stu-id="8c117-516">Multiple filter stages</span></span>

<span data-ttu-id="8c117-517">È possibile implementare interfacce per più fasi di filtro in una singola classe.</span><span class="sxs-lookup"><span data-stu-id="8c117-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8c117-518">Ad esempio, la classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e i relativi equivalenti asincroni.</span><span class="sxs-lookup"><span data-stu-id="8c117-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="8c117-519">Implementare la versione sincrona **oppure** la versione asincrona di un'interfaccia di filtro, **non** entrambe.</span><span class="sxs-lookup"><span data-stu-id="8c117-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8c117-520">Il runtime controlla per prima cosa se il filtro implementa l'interfaccia asincrona e, in tal caso, la chiama.</span><span class="sxs-lookup"><span data-stu-id="8c117-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8c117-521">In caso contrario, chiama i metodi dell'interfaccia sincrona.</span><span class="sxs-lookup"><span data-stu-id="8c117-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8c117-522">Se in una classe sono implementate entrambe le interfacce, sincrona e asincrona, viene chiamato solo il metodo asincrono.</span><span class="sxs-lookup"><span data-stu-id="8c117-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8c117-523">Quando si usano classi astratte come <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> eseguire l'override solo dei metodi sincroni o del metodo asincrono per ogni tipo di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8c117-524">Attributi filtro predefiniti</span><span class="sxs-lookup"><span data-stu-id="8c117-524">Built-in filter attributes</span></span>

<span data-ttu-id="8c117-525">ASP.NET Core include filtri predefiniti basati su attributi che è possibile impostare come sottoclasse e personalizzare.</span><span class="sxs-lookup"><span data-stu-id="8c117-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8c117-526">Il filtro dei risultati seguente, ad esempio, aggiunge un'intestazione alla risposta:</span><span class="sxs-lookup"><span data-stu-id="8c117-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-527">Gli attributi consentono ai filtri di accettare argomenti, come illustrato nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="8c117-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8c117-528">Applicare `AddHeaderAttribute` a un controller o a un metodo di azione e specificare il nome e il valore dell'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="8c117-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="8c117-529">Molte delle interfacce del filtro presentano attributi corrispondenti che possono essere usati come classi di base per implementazioni personalizzate.</span><span class="sxs-lookup"><span data-stu-id="8c117-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8c117-530">Attributi dei filtri:</span><span class="sxs-lookup"><span data-stu-id="8c117-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8c117-531">Ambiti dei filtri e ordine di esecuzione</span><span class="sxs-lookup"><span data-stu-id="8c117-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="8c117-532">È possibile aggiungere un filtro alla pipeline in uno dei tre *ambiti*:</span><span class="sxs-lookup"><span data-stu-id="8c117-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8c117-533">Usando un attributo di un'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="8c117-534">Usando un attributo di un controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="8c117-535">A livello globale per tutti i controller e le azioni come illustrato nel codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8c117-536">Il codice precedente aggiunge tre filtri a livello globale tramite la raccolta [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="8c117-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="8c117-537">Ordine di esecuzione predefinito</span><span class="sxs-lookup"><span data-stu-id="8c117-537">Default order of execution</span></span>

<span data-ttu-id="8c117-538">Quando sono presenti più filtri *dello stesso tipo*, scope determina l'ordine predefinito di esecuzione del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8c117-539">Filtri globali circondano i filtri di classe.</span><span class="sxs-lookup"><span data-stu-id="8c117-539">Global filters surround class filters.</span></span> <span data-ttu-id="8c117-540">Filtri di classe filtri di metodo Racchiudi.</span><span class="sxs-lookup"><span data-stu-id="8c117-540">Class filters surround method filters.</span></span>

<span data-ttu-id="8c117-541">Come risultato dell'annidamento dei filtri, il codice *after* dei filtri viene eseguito in ordine inverso rispetto al codice *before*.</span><span class="sxs-lookup"><span data-stu-id="8c117-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8c117-542">Sequenza di filtro:</span><span class="sxs-lookup"><span data-stu-id="8c117-542">The filter sequence:</span></span>

* <span data-ttu-id="8c117-543">Codice *before* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="8c117-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8c117-544">Codice *before* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="8c117-545">Codice *before* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8c117-546">Codice *after* dei filtri del metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8c117-547">Codice *after* dei filtri del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="8c117-548">Codice *after* dei filtri globali.</span><span class="sxs-lookup"><span data-stu-id="8c117-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8c117-549">L'esempio seguente illustra l'ordine in cui i metodi dei filtri vengono chiamati per i filtri di azione sincroni.</span><span class="sxs-lookup"><span data-stu-id="8c117-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8c117-550">Sequenza</span><span class="sxs-lookup"><span data-stu-id="8c117-550">Sequence</span></span> | <span data-ttu-id="8c117-551">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-551">Filter scope</span></span> | <span data-ttu-id="8c117-552">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8c117-553">1</span><span class="sxs-lookup"><span data-stu-id="8c117-553">1</span></span> | <span data-ttu-id="8c117-554">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-555">2</span><span class="sxs-lookup"><span data-stu-id="8c117-555">2</span></span> | <span data-ttu-id="8c117-556">Controller</span><span class="sxs-lookup"><span data-stu-id="8c117-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-557">3</span><span class="sxs-lookup"><span data-stu-id="8c117-557">3</span></span> | <span data-ttu-id="8c117-558">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-559">4</span><span class="sxs-lookup"><span data-stu-id="8c117-559">4</span></span> | <span data-ttu-id="8c117-560">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c117-561">5</span><span class="sxs-lookup"><span data-stu-id="8c117-561">5</span></span> | <span data-ttu-id="8c117-562">Controller</span><span class="sxs-lookup"><span data-stu-id="8c117-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c117-563">6</span><span class="sxs-lookup"><span data-stu-id="8c117-563">6</span></span> | <span data-ttu-id="8c117-564">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="8c117-565">Questa sequenza mostra che:</span><span class="sxs-lookup"><span data-stu-id="8c117-565">This sequence shows:</span></span>

* <span data-ttu-id="8c117-566">Il filtro del metodo è annidato all'interno del filtro del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="8c117-567">Il filtro del controller è annidato all'interno del filtro globale.</span><span class="sxs-lookup"><span data-stu-id="8c117-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="8c117-568">Filtri a Razor livello di pagina e controller</span><span class="sxs-lookup"><span data-stu-id="8c117-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="8c117-569">Ogni controller che eredita dalla classe di base <xref:Microsoft.AspNetCore.Mvc.Controller> include i metodi [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8c117-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8c117-570">Questi metodi:</span><span class="sxs-lookup"><span data-stu-id="8c117-570">These methods:</span></span>

* <span data-ttu-id="8c117-571">Eseguono il wrapping dei filtri che vengono eseguiti per una determinata azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8c117-572">La chiamata di `OnActionExecuting` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8c117-573">La chiamata di `OnActionExecuted` avviene dopo tutti i filtri dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8c117-574">La chiamata di `OnActionExecutionAsync` avviene prima di qualsiasi filtro dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8c117-575">Il codice del filtro dopo `next` viene eseguito dopo il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8c117-576">Ad esempio, l'applicazione di `MySampleActionFilter` nell'esempio scaricato avviene a livello globale all'avvio.</span><span class="sxs-lookup"><span data-stu-id="8c117-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8c117-577">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="8c117-577">The `TestController`:</span></span>

* <span data-ttu-id="8c117-578">Applica `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) all' `FilterTest2` azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8c117-579">Esegue l'override di `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="8c117-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8c117-580">Passando a `https://localhost:5001/Test/FilterTest2`, viene eseguito il codice seguente:</span><span class="sxs-lookup"><span data-stu-id="8c117-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8c117-581">Per le Razor pagine, vedere [implementare Razor filtri di pagina eseguendo l'override dei metodi di filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="8c117-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8c117-582">Override dell'ordine predefinito</span><span class="sxs-lookup"><span data-stu-id="8c117-582">Overriding the default order</span></span>

<span data-ttu-id="8c117-583">È possibile eseguire l'override della sequenza di esecuzione predefinita implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8c117-584">`IOrderedFilter` espone la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order>, che ha la precedenza sull'ambito per determinare l'ordine di esecuzione.</span><span class="sxs-lookup"><span data-stu-id="8c117-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8c117-585">Un filtro con un valore di `Order` inferiore:</span><span class="sxs-lookup"><span data-stu-id="8c117-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8c117-586">Esegue il codice *before* prima di quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="8c117-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8c117-587">Esegue il codice *after* dopo quello di un filtro con un valore di `Order` più alto.</span><span class="sxs-lookup"><span data-stu-id="8c117-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8c117-588">La proprietà `Order` può essere impostata con un parametro del costruttore:</span><span class="sxs-lookup"><span data-stu-id="8c117-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="8c117-589">Prendere in considerazione gli stessi tre filtri di azione illustrati nell'esempio precedente.</span><span class="sxs-lookup"><span data-stu-id="8c117-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="8c117-590">Se la proprietà `Order` del controller e dei filtri globali è impostata, rispettivamente, su 1 e su 2, l'ordine di esecuzione viene invertito.</span><span class="sxs-lookup"><span data-stu-id="8c117-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="8c117-591">Sequenza</span><span class="sxs-lookup"><span data-stu-id="8c117-591">Sequence</span></span> | <span data-ttu-id="8c117-592">Ambito del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-592">Filter scope</span></span> | <span data-ttu-id="8c117-593">Proprietà `Order`</span><span class="sxs-lookup"><span data-stu-id="8c117-593">`Order` property</span></span> | <span data-ttu-id="8c117-594">Metodo del filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="8c117-595">1</span><span class="sxs-lookup"><span data-stu-id="8c117-595">1</span></span> | <span data-ttu-id="8c117-596">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-596">Method</span></span> | <span data-ttu-id="8c117-597">0</span><span class="sxs-lookup"><span data-stu-id="8c117-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c117-598">2</span><span class="sxs-lookup"><span data-stu-id="8c117-598">2</span></span> | <span data-ttu-id="8c117-599">Controller</span><span class="sxs-lookup"><span data-stu-id="8c117-599">Controller</span></span> | <span data-ttu-id="8c117-600">1</span><span class="sxs-lookup"><span data-stu-id="8c117-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8c117-601">3</span><span class="sxs-lookup"><span data-stu-id="8c117-601">3</span></span> | <span data-ttu-id="8c117-602">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-602">Global</span></span> | <span data-ttu-id="8c117-603">2</span><span class="sxs-lookup"><span data-stu-id="8c117-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8c117-604">4</span><span class="sxs-lookup"><span data-stu-id="8c117-604">4</span></span> | <span data-ttu-id="8c117-605">Globale</span><span class="sxs-lookup"><span data-stu-id="8c117-605">Global</span></span> | <span data-ttu-id="8c117-606">2</span><span class="sxs-lookup"><span data-stu-id="8c117-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8c117-607">5</span><span class="sxs-lookup"><span data-stu-id="8c117-607">5</span></span> | <span data-ttu-id="8c117-608">Controller</span><span class="sxs-lookup"><span data-stu-id="8c117-608">Controller</span></span> | <span data-ttu-id="8c117-609">1</span><span class="sxs-lookup"><span data-stu-id="8c117-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8c117-610">6</span><span class="sxs-lookup"><span data-stu-id="8c117-610">6</span></span> | <span data-ttu-id="8c117-611">Metodo</span><span class="sxs-lookup"><span data-stu-id="8c117-611">Method</span></span> | <span data-ttu-id="8c117-612">0</span><span class="sxs-lookup"><span data-stu-id="8c117-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="8c117-613">La proprietà `Order` ha la precedenza sull'ambito nel determinare l'ordine di esecuzione dei filtri.</span><span class="sxs-lookup"><span data-stu-id="8c117-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8c117-614">I filtri vengono ordinati prima in base all'ordine, poi viene usato l'ambito per interrompere i collegamenti.</span><span class="sxs-lookup"><span data-stu-id="8c117-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8c117-615">Tutti i filtri predefiniti implementano `IOrderedFilter` e impostano il valore predefinito di `Order` su 0.</span><span class="sxs-lookup"><span data-stu-id="8c117-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8c117-616">Per i filtri predefiniti, l'ambito determina l'ordine, a meno che non si imposti `Order` su un valore diverso da zero.</span><span class="sxs-lookup"><span data-stu-id="8c117-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8c117-617">Annullamento e corto circuito</span><span class="sxs-lookup"><span data-stu-id="8c117-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8c117-618">È possibile causare il corto circuito della pipeline di filtro impostando la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> sul parametro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornito al metodo di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8c117-619">Ad esempio, il filtro di risorse seguente impedisce l'esecuzione del resto della pipeline:</span><span class="sxs-lookup"><span data-stu-id="8c117-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-620">Nel codice seguente sia il filtro `ShortCircuitingResourceFilter` che il filtro `AddHeader` hanno come destinazione il metodo di azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8c117-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8c117-621">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8c117-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8c117-622">Viene eseguito per primo perché è un filtro risorsa e `AddHeader` è un filtro azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8c117-623">Blocca il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8c117-624">Pertanto il filtro `AddHeader` non viene mai eseguito per l'azione `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="8c117-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8c117-625">Questo comportamento sarebbe lo stesso se entrambi i filtri venissero applicati a livello di metodo di azione, a condizione che il filtro `ShortCircuitingResourceFilter` venga eseguito prima.</span><span class="sxs-lookup"><span data-stu-id="8c117-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8c117-626">`ShortCircuitingResourceFilter` viene eseguito per primo per il tipo di filtro o per l'uso esplicito della proprietà `Order`.</span><span class="sxs-lookup"><span data-stu-id="8c117-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="8c117-627">Inserimento di dipendenze</span><span class="sxs-lookup"><span data-stu-id="8c117-627">Dependency injection</span></span>

<span data-ttu-id="8c117-628">È possibile aggiungere filtri per tipo o per istanza.</span><span class="sxs-lookup"><span data-stu-id="8c117-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8c117-629">Se viene aggiunta un'istanza, tale istanza viene usata per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8c117-630">Se viene aggiunto un tipo, il filtro è attivato dal tipo.</span><span class="sxs-lookup"><span data-stu-id="8c117-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8c117-631">Un filtro attivato dal tipo comporta:</span><span class="sxs-lookup"><span data-stu-id="8c117-631">A type-activated filter means:</span></span>

* <span data-ttu-id="8c117-632">La creazione di un'istanza per ogni richiesta.</span><span class="sxs-lookup"><span data-stu-id="8c117-632">An instance is created for each request.</span></span>
* <span data-ttu-id="8c117-633">Il popolamento di tutte le dipendenze del costruttore tramite [inserimento delle dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c117-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8c117-634">I filtri implementati come attributi e aggiunti direttamente alle classi controller o ai metodi di azione non possono avere dipendenze costruttore specificate dall'[inserimento di dipendenze](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="8c117-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8c117-635">Le dipendenze del costruttore non possono essere fornite tramite inserimento delle dipendenze in quanto:</span><span class="sxs-lookup"><span data-stu-id="8c117-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8c117-636">I parametri del costruttore devono essere forniti agli attributi nella posizione in cui vengono applicati.</span><span class="sxs-lookup"><span data-stu-id="8c117-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8c117-637">Questa è una limitazione del funzionamento degli attributi.</span><span class="sxs-lookup"><span data-stu-id="8c117-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8c117-638">I filtri seguenti supportano le dipendenze del costruttore fornite dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8c117-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementato nell'attributo.</span><span class="sxs-lookup"><span data-stu-id="8c117-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8c117-640">I filtri precedenti possono essere applicati a un controller o a un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8c117-641">I logger sono disponibili tramite l'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-641">Loggers are available from DI.</span></span> <span data-ttu-id="8c117-642">Evitare tuttavia di creare e usare filtri esclusivamente per scopi di registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8c117-643">La funzionalità di [registrazione del framework predefinita](xref:fundamentals/logging/index) in genere fornisce il necessario per la registrazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8c117-644">La registrazione aggiunta ai filtri:</span><span class="sxs-lookup"><span data-stu-id="8c117-644">Logging added to filters:</span></span>

* <span data-ttu-id="8c117-645">Deve concentrarsi su problemi del dominio di business o comportamenti specifici del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8c117-646">**Non** deve registrare azioni o altri eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="8c117-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8c117-647">I filtri predefiniti registrano azioni ed eventi del framework.</span><span class="sxs-lookup"><span data-stu-id="8c117-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8c117-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c117-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="8c117-649">I tipi di implementazione del filtro di servizi sono registrati in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8c117-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8c117-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera un'istanza del filtro dall'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8c117-651">Il codice seguente illustra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="8c117-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c117-652">Nel codice seguente l'oggetto `AddHeaderResultServiceFilter` viene aggiunto al contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8c117-653">Nel codice seguente l'attributo `ServiceFilter` recupera un'istanza del filtro `AddHeaderResultServiceFilter` dall'inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8c117-654">Quando si usa l'impostazione `ServiceFilterAttribute`, [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8c117-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8c117-655">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="8c117-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c117-656">Il runtime di ASP.NET Core non garantisce:</span><span class="sxs-lookup"><span data-stu-id="8c117-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8c117-657">Che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8c117-658">Che il filtro non verrà richiesto di nuovo dal contenitore di inserimento delle dipendenze in un momento successivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8c117-659">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="8c117-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8c117-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8c117-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c117-661">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c117-662">`CreateInstance` carica il tipo specificato dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8c117-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c117-663">TypeFilterAttribute</span></span>

<span data-ttu-id="8c117-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> è simile a <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ma il relativo tipo non viene risolto direttamente dal contenitore dell'inserimento di dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8c117-665">Viene creata un'istanza del tipo tramite <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="8c117-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8c117-666">Poiché i tipi `TypeFilterAttribute` non vengono risolti direttamente dal contenitore di inserimento delle dipendenze:</span><span class="sxs-lookup"><span data-stu-id="8c117-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8c117-667">Non è necessario che i tipi a cui viene fatto riferimento tramite `TypeFilterAttribute` siano registrati nel contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8c117-668">Le loro dipendenze vengono soddisfatte dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8c117-669">In via facoltativa, `TypeFilterAttribute` può anche accettare gli argomenti del costruttore per il tipo.</span><span class="sxs-lookup"><span data-stu-id="8c117-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8c117-670">Quando si usa l'impostazione `TypeFilterAttribute`, [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="8c117-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8c117-671">Indica che l'istanza del filtro *può* essere riutilizzata all'esterno dell'ambito della richiesta in cui è stata creata.</span><span class="sxs-lookup"><span data-stu-id="8c117-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c117-672">Il runtime di ASP.NET Core non fornisce alcuna garanzia che venga creata una singola istanza del filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8c117-673">Non si deve usare con un filtro che dipende da servizi con una durata diversa da singleton.</span><span class="sxs-lookup"><span data-stu-id="8c117-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8c117-674">L'esempio seguente illustra come passare argomenti a un tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="8c117-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8c117-675">Filtri autorizzazione</span><span class="sxs-lookup"><span data-stu-id="8c117-675">Authorization filters</span></span>

<span data-ttu-id="8c117-676">I filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="8c117-676">Authorization filters:</span></span>

* <span data-ttu-id="8c117-677">Sono i primi filtri a essere eseguiti nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8c117-678">Controllano l'accesso ai metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-678">Control access to action methods.</span></span>
* <span data-ttu-id="8c117-679">Dispongono di un metodo precedente, ma non di un metodo successivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="8c117-680">I filtri di autorizzazione personalizzati richiedono un framework di autorizzazione personalizzato.</span><span class="sxs-lookup"><span data-stu-id="8c117-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8c117-681">È preferibile configurare criteri di autorizzazione o scrivere criteri di autorizzazione personalizzati piuttosto che scrivere un filtro personalizzato.</span><span class="sxs-lookup"><span data-stu-id="8c117-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8c117-682">Il filtro di autorizzazione predefinito:</span><span class="sxs-lookup"><span data-stu-id="8c117-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="8c117-683">Chiama il sistema di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-683">Calls the authorization system.</span></span>
* <span data-ttu-id="8c117-684">Non autorizza le richieste.</span><span class="sxs-lookup"><span data-stu-id="8c117-684">Does not authorize requests.</span></span>

<span data-ttu-id="8c117-685">**Non** generare eccezioni nei filtri di autorizzazione:</span><span class="sxs-lookup"><span data-stu-id="8c117-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8c117-686">L'eccezione non verrà gestita.</span><span class="sxs-lookup"><span data-stu-id="8c117-686">The exception will not be handled.</span></span>
* <span data-ttu-id="8c117-687">I filtri di eccezione non gestiranno l'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8c117-688">È consigliabile emettere una richiesta di verifica in caso di eccezione in un filtro di autorizzazione.</span><span class="sxs-lookup"><span data-stu-id="8c117-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8c117-689">Altre informazioni sull'[autorizzazione](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="8c117-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8c117-690">Filtri risorse</span><span class="sxs-lookup"><span data-stu-id="8c117-690">Resource filters</span></span>

<span data-ttu-id="8c117-691">I filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="8c117-691">Resource filters:</span></span>

* <span data-ttu-id="8c117-692">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8c117-693">L'esecuzione racchiude la maggior parte della pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8c117-694">Solo i [filtri di autorizzazione](#authorization-filters) vengono eseguiti prima dei filtri di risorse.</span><span class="sxs-lookup"><span data-stu-id="8c117-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8c117-695">I filtri di risorse sono utili per causare il corto circuito della maggior parte della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8c117-696">Un filtro di memorizzazione nella cache può, ad esempio, evitare il resto della pipeline in caso di riscontro nella cache.</span><span class="sxs-lookup"><span data-stu-id="8c117-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8c117-697">Esempi di filtri di risorse:</span><span class="sxs-lookup"><span data-stu-id="8c117-697">Resource filter examples:</span></span>

* <span data-ttu-id="8c117-698">Il [filtro di risorse di corto circuito](#short-circuiting-resource-filter) illustrato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="8c117-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8c117-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="8c117-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8c117-700">Impedisce all'associazione di modelli di accedere ai dati del modulo.</span><span class="sxs-lookup"><span data-stu-id="8c117-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8c117-701">Viene usato quando si caricano file di grandi dimensioni per impedire la lettura dei dati del modulo in memoria.</span><span class="sxs-lookup"><span data-stu-id="8c117-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8c117-702">Filtri azione</span><span class="sxs-lookup"><span data-stu-id="8c117-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8c117-703">I filtri azione **non** si applicano alle Razor pagine.</span><span class="sxs-lookup"><span data-stu-id="8c117-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="8c117-704">RazorPages supporta <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="8c117-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8c117-705">Per ulteriori informazioni, vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8c117-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8c117-706">I filtri di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-706">Action filters:</span></span>

* <span data-ttu-id="8c117-707">Implementano l'interfaccia <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8c117-708">La loro esecuzione circonda l'esecuzione dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8c117-709">Il codice seguente mostra un filtro di azione di esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c117-710">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> specifica le proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8c117-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>: consente di leggere gli input per un metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="8c117-712"><xref:Microsoft.AspNetCore.Mvc.Controller>: consente di modificare l'istanza del controller.</span><span class="sxs-lookup"><span data-stu-id="8c117-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8c117-713"><xref:System.Web.Mvc.ActionExecutingContext.Result>: l'impostazione di `Result` causa il corto circuito del metodo di azione e dei filtri di azione successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8c117-714">La generazione di un'eccezione in un metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8c117-715">Impedisce l'esecuzione dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8c117-716">A differenza dell'impostazione di `Result`, è considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c117-717">La classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> specifica `Controller` e `Result` oltre alle proprietà seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8c117-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>: true se un altro file ha causato il corto circuito dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c117-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception>: non Null se l'azione o un filtro di azione eseguito in precedenza ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8c117-720">Se si imposta questa proprietà su Null:</span><span class="sxs-lookup"><span data-stu-id="8c117-720">Setting this property to null:</span></span>

  * <span data-ttu-id="8c117-721">L'eccezione viene gestita in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="8c117-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8c117-722">L'oggetto `Result` viene eseguito come se fosse stato restituito dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8c117-723">Per un oggetto `IAsyncActionFilter`, una chiamata a <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="8c117-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8c117-724">Esegue qualsiasi filtro azione successivo e il metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8c117-725">Restituisca il valore `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="8c117-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8c117-726">Per causare il corto circuito, assegnare <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a un'istanza del risultato e non chiamare `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="8c117-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8c117-727">Il framework fornisce un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="8c117-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8c117-728">Il filtro di azione `OnActionExecuting` può essere usato per:</span><span class="sxs-lookup"><span data-stu-id="8c117-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8c117-729">Convalidare lo stato del modello.</span><span class="sxs-lookup"><span data-stu-id="8c117-729">Validate model state.</span></span>
* <span data-ttu-id="8c117-730">Restituire un errore se lo stato non è valido.</span><span class="sxs-lookup"><span data-stu-id="8c117-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="8c117-731">Il metodo `OnActionExecuted` viene eseguito dopo il metodo di azione:</span><span class="sxs-lookup"><span data-stu-id="8c117-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8c117-732">È possibile visualizzare e modificare i risultati dell'azione tramite la proprietà <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="8c117-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8c117-733">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> è true se un altro filtro ha causato il corto circuito dell'esecuzione dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c117-734">L'impostazione di <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> è un valore non Null se l'azione o un filtro di azione successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8c117-735">Impostazione di `Exception` su null:</span><span class="sxs-lookup"><span data-stu-id="8c117-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8c117-736">Gestisce un'eccezione in modo efficace.</span><span class="sxs-lookup"><span data-stu-id="8c117-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8c117-737">L'oggetto `ActionExecutedContext.Result` viene eseguito come se fosse stato restituito normalmente dal metodo di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8c117-738">Filtri eccezioni</span><span class="sxs-lookup"><span data-stu-id="8c117-738">Exception filters</span></span>

<span data-ttu-id="8c117-739">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-739">Exception filters:</span></span>

* <span data-ttu-id="8c117-740">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="8c117-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="8c117-741">Possono essere usati per implementare criteri di gestione degli errori comuni.</span><span class="sxs-lookup"><span data-stu-id="8c117-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8c117-742">Il filtro di eccezione di esempio seguente usa una visualizzazione degli errori personalizzata per mostrare i dettagli sulle eccezioni che si verificano quando l'app è in fase di sviluppo:</span><span class="sxs-lookup"><span data-stu-id="8c117-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8c117-743">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-743">Exception filters:</span></span>

* <span data-ttu-id="8c117-744">Non dispone di eventi precedenti o successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-744">Don't have before and after events.</span></span>
* <span data-ttu-id="8c117-745">Implementano <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8c117-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8c117-746">Gestire le eccezioni non gestite che si verificano nella Razor creazione della pagina o del controller, dell' [associazione di modelli](xref:mvc/models/model-binding), dei filtri azione o dei metodi di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8c117-747">**Non** rilevano le eccezioni che si verificano nei filtri di risorse, nei filtri dei risultati o nell'esecuzione dei risultati MVC.</span><span class="sxs-lookup"><span data-stu-id="8c117-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8c117-748">Per gestire un'eccezione, impostare la proprietà <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> su `true` o scrivere una risposta.</span><span class="sxs-lookup"><span data-stu-id="8c117-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8c117-749">In questo modo si arresta la propagazione dell'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-749">This stops propagation of the exception.</span></span> <span data-ttu-id="8c117-750">Un filtro di eccezione non può modificare un'eccezione in una "operazione riuscita".</span><span class="sxs-lookup"><span data-stu-id="8c117-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8c117-751">Questa operazione può essere eseguita solo tramite un filtro di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-751">Only an action filter can do that.</span></span>

<span data-ttu-id="8c117-752">Filtri eccezioni:</span><span class="sxs-lookup"><span data-stu-id="8c117-752">Exception filters:</span></span>

* <span data-ttu-id="8c117-753">Sono ideali per intercettare le eccezioni che si verificano nelle azioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8c117-754">Non sono flessibili quanto il middleware di gestione degli errori.</span><span class="sxs-lookup"><span data-stu-id="8c117-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8c117-755">Scegliere il middleware per la gestione delle eccezioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8c117-756">Usare i filtri di eccezione solo quando la gestione degli errori *varia* in base al metodo di azione chiamato.</span><span class="sxs-lookup"><span data-stu-id="8c117-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8c117-757">Un'app può ad esempio avere metodi di azione sia per gli endpoint API che per le visualizzazioni/HTML.</span><span class="sxs-lookup"><span data-stu-id="8c117-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8c117-758">Gli endpoint dell'API possono restituire informazioni sull'errore come JSON, mentre le azioni basate sulla visualizzazione possono restituire una pagina di errore in formato HTML.</span><span class="sxs-lookup"><span data-stu-id="8c117-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8c117-759">Filtri risultato</span><span class="sxs-lookup"><span data-stu-id="8c117-759">Result filters</span></span>

<span data-ttu-id="8c117-760">I filtri dei risultati:</span><span class="sxs-lookup"><span data-stu-id="8c117-760">Result filters:</span></span>

* <span data-ttu-id="8c117-761">Implementare un'interfaccia:</span><span class="sxs-lookup"><span data-stu-id="8c117-761">Implement an interface:</span></span>
  * <span data-ttu-id="8c117-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8c117-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> o <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c117-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8c117-764">La loro esecuzione circonda l'esecuzione dei risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8c117-765">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c117-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8c117-766">Il codice seguente illustra un filtro dei risultati che aggiunge un'intestazione HTTP:</span><span class="sxs-lookup"><span data-stu-id="8c117-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c117-767">Il tipo di risultato eseguito dipende dall'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8c117-768">Un'azione che restituisce una visualizzazione include tutte le elaborazioni Razor come parte dell'elemento <xref:Microsoft.AspNetCore.Mvc.ViewResult> eseguito.</span><span class="sxs-lookup"><span data-stu-id="8c117-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8c117-769">Un metodo API può eseguire la serializzazione in quanto parte dell'esecuzione del risultato.</span><span class="sxs-lookup"><span data-stu-id="8c117-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8c117-770">Altre informazioni sui [risultati dell'azione](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="8c117-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8c117-771">I filtri dei risultati vengono eseguiti solo quando un filtro azione o azione produce un risultato di azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8c117-772">I filtri dei risultati non vengono eseguiti nei casi seguenti:</span><span class="sxs-lookup"><span data-stu-id="8c117-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="8c117-773">Un filtro di autorizzazione o un filtro risorse cortocircui la pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8c117-774">Un filtro di eccezione non gestisca un'eccezione producendo un risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8c117-775">Il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> può causare il corto circuito dell'esecuzione del risultato dell'azione e dei filtri dei risultati successivi se si imposta <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> su `true`.</span><span class="sxs-lookup"><span data-stu-id="8c117-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8c117-776">In caso di corto circuito, scrivere nell'oggetto risposta per evitare di generare una risposta vuota.</span><span class="sxs-lookup"><span data-stu-id="8c117-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8c117-777">La generazione di un'eccezione in `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="8c117-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="8c117-778">Impedisce l'esecuzione del risultato dell'azione e dei filtri successivi.</span><span class="sxs-lookup"><span data-stu-id="8c117-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8c117-779">È considerata un errore anziché un risultato positivo.</span><span class="sxs-lookup"><span data-stu-id="8c117-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c117-780">Quando <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> viene eseguito il metodo, è probabile che la risposta sia già stata inviata al client.</span><span class="sxs-lookup"><span data-stu-id="8c117-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="8c117-781">Se la risposta è già stata inviata al client, non è possibile modificarla ulteriormente.</span><span class="sxs-lookup"><span data-stu-id="8c117-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="8c117-782">L'impostazione di `ResultExecutedContext.Canceled` è `true` se un altro filtro ha causato il corto circuito dell'esecuzione del risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8c117-783">L'impostazione di `ResultExecutedContext.Exception` è un valore non Null se il risultato dell'azione o un filtro dei risultati successivo ha generato un'eccezione.</span><span class="sxs-lookup"><span data-stu-id="8c117-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8c117-784">Se si imposta `Exception` su Null l'eccezione viene gestita in modo efficace e si evita che venga generata nuovamente da ASP.NET Core in un punto successivo della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="8c117-785">Non c'è alcun modo affidabile per scrivere i dati in una risposta quando si gestisce un'eccezione in un filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="8c117-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8c117-786">Se le intestazioni sono state scaricate nel client quando il risultato di un'azione genera un'eccezione, non c'è alcun meccanismo affidabile per inviare un codice di errore.</span><span class="sxs-lookup"><span data-stu-id="8c117-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8c117-787">Per un oggetto <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, una chiamata a `await next` in <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> esegue tutti i filtri dei risultati successivi e il risultato dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8c117-788">Per causare il corto circuito, impostare [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) su `true` e non chiamare `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="8c117-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8c117-789">Il framework fornisce un oggetto `ResultFilterAttribute` astratto che è possibile impostare come sottoclasse.</span><span class="sxs-lookup"><span data-stu-id="8c117-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8c117-790">La classe [AddHeaderAttribute](#add-header-attribute) illustrata in precedenza è un esempio di un attributo di filtro dei risultati.</span><span class="sxs-lookup"><span data-stu-id="8c117-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8c117-791">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c117-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8c117-792">Le interfacce <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> dichiarano un'implementazione di <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> eseguita per tutti i risultati dell'azione.</span><span class="sxs-lookup"><span data-stu-id="8c117-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8c117-793">Sono inclusi i risultati dell'azione prodotti da:</span><span class="sxs-lookup"><span data-stu-id="8c117-793">This includes action results produced by:</span></span>

* <span data-ttu-id="8c117-794">Filtri di autorizzazione e filtri delle risorse che interessano il cortocircuito.</span><span class="sxs-lookup"><span data-stu-id="8c117-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8c117-795">Filtri eccezioni.</span><span class="sxs-lookup"><span data-stu-id="8c117-795">Exception filters.</span></span>

<span data-ttu-id="8c117-796">Ad esempio, il filtro seguente viene eseguito sempre e imposta un risultato dell'azione (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) con un codice di stato *422 Entità non elaborabile* quando la negoziazione del contenuto ha esito negativo:</span><span class="sxs-lookup"><span data-stu-id="8c117-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8c117-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c117-797">IFilterFactory</span></span>

<span data-ttu-id="8c117-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8c117-799">Pertanto, un'istanza di `IFilterFactory` può essere usata come un'istanza di `IFilterMetadata` in un punto qualsiasi della pipeline filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8c117-800">Quando il runtime si prepara per richiamare il filtro, cerca di eseguirne il cast a un oggetto `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="8c117-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8c117-801">Se l'esecuzione del cast ha esito positivo, viene chiamato il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare l'istanza di `IFilterMetadata` richiamata.</span><span class="sxs-lookup"><span data-stu-id="8c117-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8c117-802">In questo modo viene specificata una struttura flessibile, poiché non è necessario impostare in modo esplicito la pipeline filtro all'avvio dell'app.</span><span class="sxs-lookup"><span data-stu-id="8c117-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8c117-803">Un altro approccio alla creazione di filtri consiste nell'implementare `IFilterFactory` usando implementazioni dell'attributo personalizzate:</span><span class="sxs-lookup"><span data-stu-id="8c117-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8c117-804">Il codice precedente può essere testato eseguendo l'[esempio scaricato](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="8c117-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="8c117-805">Richiamare gli strumenti di sviluppo F12.</span><span class="sxs-lookup"><span data-stu-id="8c117-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8c117-806">Accedere a `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="8c117-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8c117-807">Gli strumenti di sviluppo F12 visualizzano le intestazioni di risposta seguenti aggiunte dal codice di esempio:</span><span class="sxs-lookup"><span data-stu-id="8c117-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8c117-808">**Autore:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="8c117-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="8c117-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8c117-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8c117-810">**interno:**`My header`</span><span class="sxs-lookup"><span data-stu-id="8c117-810">**internal:** `My header`</span></span>

<span data-ttu-id="8c117-811">Il codice precedente crea l'intestazione di risposta **internal:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="8c117-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8c117-812">Implementazione di IFilterFactory in un attributo</span><span class="sxs-lookup"><span data-stu-id="8c117-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8c117-813">I filtri che implementano `IFilterFactory` sono utili per i filtri che:</span><span class="sxs-lookup"><span data-stu-id="8c117-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8c117-814">Non richiedono il passaggio di parametri.</span><span class="sxs-lookup"><span data-stu-id="8c117-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="8c117-815">Hanno dipendenze del costruttore che devono essere soddisfatte dall'inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8c117-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="8c117-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c117-817">`IFilterFactory` espone il metodo <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> per creare un'istanza <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="8c117-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c117-818">`CreateInstance` carica il tipo specificato dal contenitore di inserimento delle dipendenze.</span><span class="sxs-lookup"><span data-stu-id="8c117-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8c117-819">Il codice seguente illustra tre approcci all'applicazione di `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="8c117-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8c117-820">Nel codice precedente la decorazione del metodo con `[SampleActionFilter]` è l'approccio da preferire per applicare `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="8c117-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8c117-821">Uso di middleware nella pipeline filtro</span><span class="sxs-lookup"><span data-stu-id="8c117-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8c117-822">I filtri risorse funzionano come [middleware](xref:fundamentals/middleware/index) in quanto racchiudono l'esecuzione di tutto ciò che viene dopo nella pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8c117-823">I filtri sono diversi dal middleware in quanto fanno parte del runtime di ASP.NET Core, quindi hanno accesso al contesto e ai costrutti di ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8c117-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="8c117-824">Per usare il middleware come filtro, creare un tipo con un metodo `Configure` che specifica il middleware da inserire nella pipeline di filtro.</span><span class="sxs-lookup"><span data-stu-id="8c117-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8c117-825">L'esempio seguente usa il middleware di localizzazione per stabilire le impostazioni cultura correnti per una richiesta:</span><span class="sxs-lookup"><span data-stu-id="8c117-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8c117-826">Usare <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> per eseguire il middleware:</span><span class="sxs-lookup"><span data-stu-id="8c117-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8c117-827">I filtri middleware vengono eseguiti nella stessa fase della pipeline filtro come filtri risorse, prima dell'associazione di modelli e dopo il resto della pipeline.</span><span class="sxs-lookup"><span data-stu-id="8c117-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8c117-828">Azioni successive</span><span class="sxs-lookup"><span data-stu-id="8c117-828">Next actions</span></span>

* <span data-ttu-id="8c117-829">Vedere [metodi di filtro per le Razor pagine](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="8c117-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8c117-830">Per sperimentare i filtri, [scaricare, testare e modificare l'esempio di GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="8c117-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
